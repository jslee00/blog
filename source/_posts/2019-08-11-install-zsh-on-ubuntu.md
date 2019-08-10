---
title: Ubuntu에 Zsh 구성하기
date: 2019-08-11 01:35:41
updated:
categories:
  - 개발 환경 구축
tags:
  - Zsh
  - Terminal
---

## 개요

{% asset_img "terminal.svg" 200 %}

개발 일을 하는 사람에게 뗄래야 뗄 수 없는 것이 바로 터미널 소프트웨어이다.
이왕 계속 보는 거 좀 이쁘면 기분이 좋지 않을까?

나와 같은 생각을 했던 많은 개발자들이 탄탄대로를 만들어 놓았다.
이 문서에서는 Ubuntu 환경에 Zsh를 설치하고 구성하는 방법을 정리한다.

<!-- more -->

## Zsh 설치

다음 명령어를 이용하여 설치할 수 있다.

```bash
sudo apt-get install zsh
```

다음 명령어를 이용하여 zsh를 현재 계정의 기본 쉘로 변경할 수 있다.

```bash
chsh -s /usr/bin/zsh
```

## oh-my-zsh 구성

### oh-my-zsh 설치

다음 명령어를 이용하여 설치할 수 있다.

```bash
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

### 테마 변경

다음과 같이 `~/.zshrc` 파일을 수정하여 `agnoster` 테마를 사용하도록 설정한다.

```bash
THEME="agnoster"
```

다음 명령어를 이용하여 변경사항을 반영한다.

```bash
source ~/.zshrc
```

폰트가 깨질 경우 Powerline을 지원하는 폰트를 설치한다.

필자는 D2Coding 폰트를 설치하였다.

폰트 설치 방법은 추후 별도의 포스트로 정리하도록 하겠다.

### Agnoster 테마 컴퓨터 이름 숨기기 설정

Agnoster 테마는 기본적으로 매번 입력을 받을 때마다 계정 이름과 컴퓨터 이름을 표시한다.

이 때문에 조금만 긴 명령어를 입력하고자 하면 쉽게 라인을 넘어가는 경우가 생긴다.

조금 불편하기 때문에 컴퓨터 이름을 표시하지 않게 설정하여 이런 경우를 줄일 수 있다.

다음 구문을 `~/.zshrc` 파일에 추가한다.

```bash
prompt_context() {
  if [[ "$USER" != "$DEFAULT_USER" || -n "$SSH_CLIENT" ]]; then
    prompt_segment black default "%(!.%{%F{yellow}%}.)$USER"
  fi
}
```

### New Line 설정

Agnoster 테마는 입력 라인에 표시하는 정보가 많아 조금만 긴 명령어를 입력하면 쉽게 라인을 넘어간다.

입력을 다음 라인부터 받게 하여 이러한 경우를 줄일 수 있다.

`~/.oh-my-zsh/themes/agnoster.zsh-theme` 파일을 다음과 같이 수정한다.

```bash
### Main prompt
build_prompt() {
  RETVAL=$?
  prompt_status
  prompt_virtualenv
  prompt_aws
  prompt_context
  prompt_dir
  prompt_git
  prompt_bzr
  prompt_hg
  prompt_newline
  prompt_end
}
```

```bash
prompt_newline() {
  if [[ -n $CURRENT_BG ]]; then
    echo -n " %{%k%F{$CURRENT_BG}%}$SEGMENT_SEPARATOR
    %(?.%F{$CURRENT_BG}.%F{red})❯%f"
  else
    echo -n "%{%k%}"
  fi

  echo -n "%{%f%}"
  CURRENT_BG=''
}
```

### zsh-autosuggestions 플러그인 설치

[zsh-autosuggestions GitHub Repository](https://github.com/zsh-users/zsh-autosuggestions) 페이지를 참고하여 플러그인을 설치한다.

다음과 같이 `~/.zshrc` 파일을 수정하여 설치한 플러그인을 적용한다.

```bash
plugins=(
  ...
  zsh-autosuggestions
)
```

다음 명령어를 이용하여 변경사항을 반영한다.

```bash
source ~/.zshrc
```

### zsh-syntax-highlighting 플러그인 설치

[zsh-syntax-highlighting GitHub Repository](https://github.com/zsh-users/zsh-syntax-highlighting) 페이지를 참고하여 플러그인을 설치한다.

다음과 같이 `~/.zshrc` 파일을 수정하여 설치한 플러그인을 적용한다.

```bash
plugins=(
  ...
  zsh-syntax-highlighting
)
```

다음 명령어를 이용하여 변경사항을 반영한다.

```bash
source ~/.zshrc
```

### autojump 플러그인 설치

다음 명령어를 이용하여 플러그인을 설치한다.

```bash
sudo apt-get install autojump
```

다음과 같이 `~/.zshrc` 파일을 수정하여 설치한 플러그인을 적용한다.

```bash
plugins=(
  ...
  autojump
)
```

다음 명령어를 이용하여 변경사항을 반영한다.

```bash
source ~/.zshrc
```

## 참고자료

- [Icons made by Smashicons CC 3.0 BY](https://www.flaticon.com/authors/smashicons)
- [우분투에 zsh와 oh my zsh 설치하기](https://the-illusionist.me/47)
- [Oh My ZSH+ iTerm2로 터미널을 더 강력하게](https://medium.com/harrythegreat/oh-my-zsh-iterm2%EB%A1%9C-%ED%84%B0%EB%AF%B8%EB%84%90%EC%9D%84-%EB%8D%94-%EA%B0%95%EB%A0%A5%ED%95%98%EA%B2%8C-a105f2c01bec)
