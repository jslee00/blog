---
title: CentOS에 JDK 설치하기
date: 2019-08-04 13:33:35
updated:
categories:
  - 시스템 구축
tags:
  - CentOS
  - Java
  - JDK
---

## 개요

CentOS 환경에 Java를 설치하는 방법을 정리한다.

## OpenJDK

### OpenJDK 버전

2019년 07월 29일 기준으로 최신 버전인 build 12+32를 이용하여 설명한다.

### OpenJDK 다운로드

[http://jdk.java.net](http://jdk.java.net/)

위 사이트에서 JDK 최신 버전의 링크를 복사한다.

본 문서 작성일 기준으로 다음과 같았다.

[https://download.java.net/openjdk/jdk12/ri/openjdk-12+32_linux-x64_bin.tar.gz](https://download.java.net/openjdk/jdk12/ri/openjdk-12+32_linux-x64_bin.tar.gz)

다음 명령어로 해당 JDK 압축 파일을 다운로드한다.

```bash
wget https://download.java.net/openjdk/jdk12/ri/openjdk-12+32_linux-x64_bin.tar.gz
```

### OpenJDK 설치

다음 명령어로 다운로드 한 압축 파일의 압축을 해제한다.

```bash
tar -xzvf openjdk-12+32_linux-x64_bin.tar.gz
```

다음 명령어로 JDK 디렉토리를 이동하고 심볼릭 링크를 생성한다.

```bash
mv jdk-12 /usr/local/
cd /usr/local
ln -sf jdk-12 java
```

사용 중인 쉘의 rc 파일의 가장 아래에 다음 구문을 추가한다. (.bashrc, .zshrc 등)

```bash
PATH=$PATH:/usr/local/java/bin
export PATH
```

다음 명령어로 사용 중인 쉘의 rc 파일을 적용한다.

```bash
source ~/.bashrc
```

다음 명령어로 Java와 컴파일러를 정상적으로 사용할 수 있는지 확인한다.

```bash
java --version
javac --version
```

## Oracle JDK

2018년 7월부터 Oracle JDK의 Java SE Subscription 라이센스 정책이 시행됨에 따라 상업용으로 이용하려면 라이센스 구독을 해야 한다.

다만 Oracle JDK 1.8.202 버전 이하는 여전히 프로그램의 동작을 위한 목적에 한하여 무료로 사용 가능한 Binary Code License(BCL) 정책을 따른다.

### Oracle JDK 버전

개인용으로 사용할 것인지 상업용으로 사용할 것인지 등에 따라서 원하는 버전이 달라질 수 있다.

필자는 개인적으로 사용할 예정이므로 2019년 08월 04일 기준으로 최신 버전인 12.0.2를 기준으로 설명한다.

### Oracle JDK 다운로드

[오라클 Java SE 다운로드 페이지](https://www.oracle.com/technetwork/java/javase/downloads/index.html)에 접속하여 원하는 버전을 다운로드한다.

오라클 웹 페이지에서는 접속하는 클라이언트가 쿠키 정책에 동의해야만 이후 작업을 처리해 주기 때문에 wget으로 간단하게 다운로드 하기 어렵다.

그래서 그냥 로컬 컴퓨터로 다운로드 하고 설치하려는 서버에 직접 옮기는 게 편하다.

### Oracle JDK 설치

다음 명령어로 다운로드 한 압축 파일의 압축을 해제한다.

```bash
tar -xzvf jdk-12.0.2_linux-x64_bin.tar.gz
```

다음 명령어로 JDK 디렉토리를 이동하고 심볼릭 링크를 생성한다.

```bash
mv jdk-12.0.2 /usr/local/
cd /usr/local
ln -sf jdk-12.0.2 java
```

사용 중인 쉘의 rc 파일의 가장 아래에 다음 구문을 추가한다. (.bashrc, .zshrc 등)

```bash
PATH=$PATH:/usr/local/java/bin
export PATH
```

다음 명령어로 사용 중인 쉘의 rc 파일을 적용한다.

```bash
source ~/.bashrc
```

다음 명령어로 Java와 컴파일러를 정상적으로 사용할 수 있는지 확인한다.

```bash
java --version
javac --version
```
