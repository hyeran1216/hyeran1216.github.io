---
layout: post
title:  "Spring Boot와 MySQL 연결 시 오류"
date:   2025-02-20 21:00 +09:00
categories: Error
permalink: /2025-02/spring-error/2
---

# Could not find mysql:mysql-connector-java

### 에러 내용
```bash
> Task :compileJava FAILED

Execution failed for task ':compileJava'.
> Could not resolve all files for configuration ':compileClasspath'.
   > Could not find mysql:mysql-connector-java:9.0.
     Required by:
         project :

Possible solution:
 - Declare repository providing the artifact, see the documentation at https://docs.gradle.org/current/userguide/declaring_repositories.html
```

### 에러 원인

의존성 문제인 것 같아서 버전도 붙여보고 별 거 다 해봤지만 계속 안돼서 구글링 했다

원래

`build.gradle`  
```java
implementation 'mysql:mysql-connector-java'
runtimeOnly 'mysql:mysql-connector-java'
```
로 의존성을 추가했었는데 그 사이에 뭐가 바뀌었나보다
### 해결 방법

8.0.31 버전 이후부터는 이렇게 해야 에러가 안 난다
```java
implementation 'com.mysql:mysql-connector-j'
runtimeOnly 'com.mysql:mysql-connector-j'
```


