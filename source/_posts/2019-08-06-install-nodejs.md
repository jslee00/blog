---
title: Node.js 설치하기
date: 2019-08-06 20:21:42
updated:
categories:
  - 시스템 구축
tags:
  - Node.js
---

## 개요

{% asset_img "nodejs-logo.svg" 250 %}

[https://github.com/nodesource/distributions/blob/master/README.md](https://github.com/nodesource/distributions/blob/master/README.md)

위 페이지를 참고하여 Node.js를 설치하는 공식적인 방법을 정리한다.

<!-- more -->

## 설치

다음 명령어를 이용하여 각 환경에서 Node.js를 설치할 수 있다.

### Ubuntu 또는 Debian 기반

#### U/D 기반 Node.js v12.x 설치

```bash
# Using Ubuntu
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt-get install -y nodejs

# Using Debian, as root
curl -sL https://deb.nodesource.com/setup_12.x | bash -
apt-get install -y nodejs
```

#### U/D 기반 Node.js v11.x 설치

```bash
# Using Ubuntu
curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash -
sudo apt-get install -y nodejs

# Using Debian, as root
curl -sL https://deb.nodesource.com/setup_11.x | bash -
apt-get install -y nodejs
```

#### U/D 기반 Node.js v10.x 설치

```bash
# Using Ubuntu
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs

# Using Debian, as root
curl -sL https://deb.nodesource.com/setup_10.x | bash -
apt-get install -y nodejs
```

### Enterprise Linux 기반 (CentOS, RHEL, Fedora 등)

#### EL 기반 Node.js v12.x 설치

```bash
curl -sL https://rpm.nodesource.com/setup_12.x | bash -
```

#### EL 기반 Node.js v11.x 설치

```bash
curl -sL https://rpm.nodesource.com/setup_11.x | bash -
```

#### EL 기반 Node.js v10.x 설치

```bash
curl -sL https://rpm.nodesource.com/setup_10.x | bash -
```
