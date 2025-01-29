---
layout: post
title:  "스프링을 실행하면 발생하는 에러"
date:   2025-01-29 23:00 +09:00
categories: Error
permalink: /2025-01/spring-error/1
---

# Web server failed to start. Port 8080 was already in use

- 에러 내용
```bash
Web server failed to start. Port 3000 was already in use.
Action:
Identify and stop the process that's listening on port 3000 or configure this application to listen on another port.
2019-11-06 22:00:06.094  INFO 8996 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Shutting down ExecutorService 'applicationTaskExecutor'
```

- 에러 원인

포트가 이미 실행 중일 때 스프링을 실행하면 발생하는 에러

- 해결 방법

실행중인 포트 끊어주기

```bash
lsof -i tcp:8080
```
나온 PID 확인 후
```bash
sudo kill -9 {PID 번호}
```