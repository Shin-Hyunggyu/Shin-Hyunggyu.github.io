---
layout: post
categories: Linux
tags: [ linux, bash, profile ]
title: "환경변수 등록"
# author: "Lofty87" (default site.author.name)
# author-img: /assets/img/<author>.jpg (default site.author.img)
description: "환경변수 등록은 login shell 과 none login shell 로 구분되고 전역(global) 등록과 지역(user) 등록으로 나누어 진다. login shell 은 아이디와 패스워드를 입력하고 로그인 후 세션이 생성될 때 처음 실행되는 shell 인데 예를 들면 putty 같은 클라이언트 프로그램을 통해 ssh 접속을 하는 것일 수 있다. none login shell 은 이미 로그인 상태이거나 세션이 없을 때 실행되는 shell 인데 예를 들면 GUI 에서 터미널을 열어 shell 이 실행되거나 루트접속 시도(su)를 통해 shell 이 실행되는 것일 수 있다. 전역(global) 등록은 모든 유저에게 공통으로 적용되는 등록이고 지역(user) 등록은 접속한 유저(본인)에게만 적용되는 등록이다."
date: 2018-10-01
---

환경변수 등록은 **login shell** 과 **none login shell** 로 구분되고 **전역(global)** 등록과 **지역(user)** 등록으로 나누어 진다. **login shell** 은 아이디와 패스워드를 입력하고 로그인 후 세션이 생성될 때 처음 실행되는 shell 인데 예를 들면 putty 같은 클라이언트 프로그램을 통해 ssh 접속을 하는 것일 수 있다. **none login shell** 은 이미 로그인 상태이거나 세션이 없을 때 실행되는 shell 인데 예를 들면 GUI 에서 터미널을 열어 shell 이 실행되거나 루트접속 시도(su)를 통해 shell 이 실행되는 것일 수 있다. **전역(global)** 등록은 모든 유저에게 공통으로 적용되는 등록이고 **지역(user)** 등록은 접속한 유저(본인)에게만 적용되는 등록이다.

<br>

### 1. login shell

login shell 이 실행될 때 함께 로드되는 파일은 '**/etc/profile**', '**~/.profile**', '**~/.bash_profile**' 등이 있다. '**/etc/profile**', '**~/.profile**' 은 모든 shell 이 대상이고 '**~/.bash_profile**' 은 bash shell 이 대상이다. 또 다른 차이는 '**/etc/profile**' 은 전역(global) 등록이고 '**~/.profile**', '**~/.bash_profile**' 은 지역(user) 등록이다. 전역(global) 등록 파일은 `/etc` 디렉토리에 있고 지역(user) 파일은 유저의 `home(~)` 디렉토리에 있다.

> 유저가 ssh 접속 후 bash shell 을 실행할 때 '/etc/profile', '~/.profile', '~/.bash_profile' 순서로 로드될 것이다.

<br>

### 2. none login shell

none login shell (bash shell) 이 실행될 때 로드되는 파일은 '**/etc/bashrc**', '**~/.bashrc**' 등이 있다. 역시 이 둘의 차이도 전역(global) 등록과 지역(user) 등록의 차이이다. '**/etc/bashrc**' 에 등록된 환경변수는 모든 유저에게 공통으로 로드될 것이고 '**~/.bashrc**' 에 등록된 환경변수는 접속한 유저(본인)에게만 로드될 것이다.

<br>

### 3. 결론

따라서 putty 같은 클라이언트 프로그램을 통해 ssh 접속 후 사용되어야 할 환경변수는 '**/etc/profile**', '**~/.profile**', '**~/.bash_profile**' 에 shell 종류와 적용대상에 맞게 등록하면 되겠다.

```bash
export name=value
```

value 에 띄어쓰기가 있을 경우 반드시 **"(쌍따옴표)**로 감싸줘야 한다. 또 shell 에서 명령어를 실행하여 환경변수를 바로 등록할 수 도 있지만 해당 세션이 끊길 경우 환경변수는 증발되므로 유의해야 한다. 등록된 환경변수는 아래 명령어로 확인할 수 있다.

```bash
export 또는 env
```