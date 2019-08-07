---
title: Docker 데이터 디렉토리 변경
date: 2019-08-07 20:58:33
updated:
categories:
  - 시스템 구축
tags:
  - Docker
---

## 개요

Docker는 기본적으로 `/var/lib/docker` 디렉토리에 Docker Image 등의 데이터를 저장한다.
이 상태로 두면 서버의 루트 용량이 적을 경우 Docker의 데이터로 인해 루트가 가득 차 각종 오류를 일으킬 수 있다.

따라서 Docker 데이터 디렉토리를 큰 용량을 가진 별도의 디렉토리로 변경할 필요가 있다.
이 페이지에서는 `/home/docker` 디렉토리로 변경한다.

<!-- more -->

## 방법

### Ubuntu/Debian Docker 데이터 디렉토리 변경

먼저 다음 명령어를 이용하여 실행 중인 Docker 프로세스를 중지한다.

```bash
sudo service docker stop
```

`/etc/default/docker` 파일을 다음과 같이 수정한다.

```bash
DOCKER_OPTS="-g /home/docker"
```

기존 자료를 다음 명령어로 이동한다.

```bash
mv /var/lib/docker/* /home/docker/
```

다음 명령어를 이용하여 Docker 프로세스를 실행한다.

```bash
sudo service docker start
```

### CentOS Docker 데이터 디렉토리 변경

먼저 다음 명령어를 이용하여 실행 중인 Docker 프로세스를 중지한다.

```bash
sudo systemctl stop docker
```

`/usr/lib/systemd/system/docker.service` 파일을 다음과 같이 수정한다.

```conf
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target
[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd -g /home/docker
ExecReload=/bin/kill -s HUP $MAINPID
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
#TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
# restart the docker process if it exits prematurely
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s
[Install]
WantedBy=multi-user.target
```

ExecStart 부분의 맨 끝에 -g /home/docker 를 추가하였다.

기존 자료를 다음 명령어로 이동한다.

```bash
mv /var/lib/docker/* /home/docker/
```

다음 명령어를 이용하여 Docker 프로세스를 실행한다.

```bash
sudo systemctl start docker
```

## 참고자료

- [Docker 설치 후 이미지 보관 디렉토리 변경](http://dveamer.github.io/backend/DockerImageDirectory.html)
- [Set Docker_Opts in centos](https://stackoverflow.com/questions/26166550/set-docker-opts-in-centos)
