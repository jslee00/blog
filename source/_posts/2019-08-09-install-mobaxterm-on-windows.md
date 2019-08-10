---
title: Windows에 MobaXterm 설치하기
date: 2019-08-09 13:24:41
updated:
categories:
  - 개발 환경 구축
tags:
  - SSH
  - Windows
  - MobaXterm
  - Terminal
  - X11
---

## 개요

{% asset_img "mobaxterm-logo.jpg" 200 %}

보통 컴퓨터 사면 게임도 해야 되고 인터넷 뱅킹도 해야 되고 하니 거의 무조건 Windows를 설치한다.
근데 소프트웨어를 구동하고 서비스하기 위한 서버에서는 서버 구축하기에 편하도록 리눅스를 사용하는 곳이 많다.
그리고 한 서버에 여러 명이 작업을 해야 하기 때문에 SSH라는 기술을 이용해서 원격으로 접속한다.

<!-- more -->

리눅스나 macOS의 경우 기본 내장된 터미널 도구를 이용하여 서버에 SSH로 접속할 수 있다.
그런데 Windows는 기본 내장된 터미널이 CMD, Powershell 이런 것들이라 서버에 접속하는 등의 일이 잘 안 된다.

MobaXterm은 X11 서버 등의 여러 도구를 포함한 SSH 클라이언트 소프트웨어다.
Windows 환경에서 실행할 수 있으며 나도 직접 쓰고 있는 도구이다.
넷사랑 사의 Xmanager 등과 비슷한 위치에 있는 도구라고 이해하면 된다.
하지만 Xmanager 등은 유료 소프트웨어인데 반해 MobaXterm은 GNU 라이센스를 따르기 때문에 무료 버전은 회사에서 써도 문제가 없다.
그래서 Windows 환경을 사용하고 있다면 안 쓸 이유가 없다.

이 글에서는 MobaXterm을 설치하고 사용하는 방법에 대하여 정리한다.

## 설치

MobaXterm은 [공식 홈페이지](https://mobaxterm.mobatek.net/)가 있다.

공식 홈페이지에서 Download 탭을 선택하고 무료인 Home Edition을 다운로드한다.

Portable 버전과 Installer 버전이 있는데 그냥 실행하든 설치해서 실행하든 별 차이 없으니 원하는 대로 고르면 된다.

그럼 설치는 끝난다.

## 실행

설치된 MobaXterm을 실행하면 Home 탭이 열려있는 창이 뜬다.

### SSH 서버 접속

좌측 Sessions 탭에 오른쪽 클릭을 하고 New session을 선택한다.

세션을 만드는 창이 뜨면 SSH를 선택하고 서버 주소와 포트, 사용할 계정을 입력한 후 OK를 누른다.

Sessions 탭에 만들어진 항목을 더블클릭하면 새로운 탭이 생기면서 접속이 된다.

비밀번호를 치면 기억할지 물어보는데 Yes를 선택하면 다음부터는 비밀번호를 안 물어본다.

### WSL 접속

MobaXterm으로 WSL에 접속할 수도 있다.

세션을 만들 때 WSL을 선택하고 본인이 설치한 리눅스 배포판을 선택한다.

OK를 누르고 만들어진 항목을 더블클릭하면 WSL에 접속이 된다.

### SSH Tunnel

MobaXterm에 SSH Tunnel 도구가 있다.

상단 도구 모음에 Tunneling이라는 라벨이 붙어 있는 항목을 선택하면 새 창이 뜨고 SSH Tunneling을 설정할 수 있다.

SSH Tunneling에 대한 자세한 내용은 따로 정리해서 올리도록 하겠다.

### 저장된 비밀번호 관리

Settings → General → MobaXterm passwords management 항목을 선택하면

저장된 비밀번호를 확인하고 삭제하고 내보내는 등의 관리를 할 수가 있다.

## 문제 해결

### Home, End키가 안 먹는 문제

MobaXterm으로 접속하면 Home키를 이용한 라인 맨 앞으로 가기와 End키를 이용한 라인 맨 뒤로 가기 기능이 안 되는 경우가 있다.

이 때는 사용 중인 쉘의 rc 스크립트에 다음 구문을 추가해주면 된다.

```bash
bindkey '^[[1~' beginning-of-line
bindkey '^[[4~' end-of-line
```
