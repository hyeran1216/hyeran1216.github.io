---
layout: post
title:  "aws - computing"
date:   2025-04-13 23:00 +09:00
categories: BE
---
# AWS 컴퓨팅 실습 기록

### 실습 목표

1. FastAPI 앱을 Docker 컨테이너로 EC2에서 실행
2. Application Load Balancer(ALB)와 Target Group 설정
3. Launch Template 및 Auto Scaling Group 구성
4. ab (ApacheBench)를 통한 부하 테스트 수행
5. Auto Scaling에 따라 인스턴스 자동 확장 확인
6. 리소스 비용 방지를 위한 마무리 정리
<br>
<br>
### EC2 인스턴스 생성 및 Docker 세팅

- EC2 생성
    - AWS 콘솔 > EC2 > 인스턴스 시작
    - Ubuntu 22.04 선택
    - 인스턴스 유형: t2.micro
    - 키페어 새로 생성
        - 로컬에서 .pem 키 파일 (예: private.pem) 다운로드 및 chmod 400 설정
        - `chmod 400 ~/Downloads/private.pem`


    - 네트워크: 기본 VPC, 퍼블릭 서브넷 선택
    - 퍼블릭 IP 자동 할당: 활성화
    - 보안 그룹 설정:
        - SSH (22), 본인 IP 허용
        - HTTP (80), 0.0.0.0/0
        - HTTPS (80), 0.0.0.0/0


- EC2 접속 후 Docker 설치
    ```shell
    sudo apt update
    sudo apt install -y docker.io
    sudo systemctl start docker
    ```


### FastAPI 앱 작성 및 Dockerfile 작성
```shell
mkdir fastapi-app && cd fastapi-app

# app/main.py 생성
cat <<EOF > main.py
from fastapi import FastAPI
app = FastAPI()
@app.get("/") def read_root():
return {"message": "Hello, ECS & FastAPI"}
EOF

# Dockerfile 생성
cat <<EOF > Dockerfile
FROM python:3.10-slim
WORKDIR /app COPY main.py .
RUN pip install fastapi uvicorn
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
EOF

# Docker 이미지 빌드 및 실행
docker build -t fastapi-app .
docker run -d -p 80:80 --restart always fastapi-app

# 정상 확인
curl -i http://localhost/
```


### 도커 허브 푸시 (멀티 아키텍처)
EC2는 amd64 아키텍처이므로 M1/M2 Mac에서 빌드할 땐 반드시 buildx 사용해야 함
<br>
<br>
아니면 `no matching manifest for linux/amd64` 에러 발생

```shell
# Dockerfile, main.py를 Mac으로 복사
scp -i ~/Downloads/private.pem ubuntu@<EC2 IP>:~/fastapi-app/* ~/Desktop/fastapi-app/

# 빌드 및 푸시
cd ~/Desktop/fastapi-app
docker buildx build --platform linux/amd64,linux/arm64 -t username/fastapi-app:latest --push .
```


### Launch Template 생성
EC2 콘솔 → Launch Template → 새 템플릿 생성

애플리케이션 및 OS 이미지: 최근 사용 > 현재 사용중

인스턴스: t2.micro

키페어: 쓰던 키페어

User data 설정:
```bash
#!/bin/bash
sleep 30
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
docker run -d -p 80:80 --restart always username/fastapi-app:latest
```

보안 그룹: 80, 22 포트 허용


### Load Balancer 설정 (ALB)

1. EC2 → Load Balancers → 생성
2. 유형: 인스턴스
3. 리스너: HTTP 80
4. VPC 및 퍼블릭 서브넷 선택
5. 상태검사(Health check)

    프로토콜: HTTP

    경로: /

6. Application Load Balancer 생성

    체계: 인터넷 경계

    퍼블릭 서브넷 2개 이상 선택

    대상 그룹 생성


### Auto Scaling Group 설정
1. EC2 콘솔 > Auto Scaling Group > 생성
2. 이름: fastapi-asg
3. Launch Template 선택
4. 네트워크: 퍼블릭 서브넷 2개 이상 선택
5. 로드밸런싱: 기존 로드밸런서에 연결 > 로드 밸런서 대상 그룹에서 선택
6. 최소: 1 / 최대: 3 / 시작 용량: 1


### 부하 테스트 (ab)
```shell
# 로컬에서 실행
brew install httpd

ab -n 10000 -c 100 http://<ALB-DNS>/
```
- 확인 사항
    - Target Group > 등록 대상 → 인스턴스 수 증가
    - 상태 Healthy 유지 확인


### 주요 오류 & 해결 과정

| 오류 메시지 | 원인 | 해결 방법 |
|-------------|------|------------|
| `Permission denied (publickey)` | 키 경로 또는 권한 문제 | `.pem` 파일 권한 `chmod 400`, 경로 확인 |
| `no matching manifest for linux/amd64` | Docker 이미지 아키텍처 불일치 | `buildx`로 멀티 아키텍처 이미지 빌드 및 푸시 |
| `curl: Failed to connect to localhost port 80` | 컨테이너가 비정상 종료됨 | 컨테이너 재시작 (`docker run ...`) |
| Target Group 상태 Unhealthy | FastAPI 미실행 / 포트 불일치 | 컨테이너가 80포트로 실행 중인지 확인 (`docker ps`, `curl localhost`) |
| `scp: ~/Dockerfile: No such file or directory` | 경로 오기 또는 잘못된 호스트 | EC2에서 정확한 파일 경로 확인 후 복사 |


