---
layout: post
categories: Etc
tags: [ windows, wsl, linux, zsh ]
title: "Windows 에서 Linux 사용하기"
# author: "Lofty87" (default site.author.name)
# author-img: /assets/img/<author>.jpg (default site.author.img)
date: 2018-09-12
---

**WSL(Windows Subsystem for Linux)** 은 기존에 사용하던 가상머신(VMware, VirtualBox, Docker)이나 멀티부팅을 사용하지 않고도 Windows 위에 다이렉트로 Linux 환경을 만들어 준다. 이제는 Cmd, PowerShell, GitBash 를 사용하지 않고도 Bash 를 사용할 수 있게 되었으며 Linux 환경에서 실행할 수 있는 대부분의 유틸리티, 애플리케이션들도 실행할 수 있다. 현재 지원하고 있는 Linux 는 'Ubuntu', 'OpenSUSE', 'SLES', 'Kali Linux', 'Debian GNU/Linux' 가 있다.

<br>

# 1. 설치하기

## 1. 시스템 요구사항

* Windows 10 (64비트)
* OS Build 16215 이상

![system-info](/assets/img/posts/Etc/2018-09-12/system-info.png)

## 2. Windows Subsystem for Linux 사용 설정

아래 두 가지 방법 중 하고 싶은 방법으로 설정하고 재부팅하면 **WSL** 을 사용할 수 있게 된다.

* **PowerShell (관리자 권한으로 실행)**

```bash
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

* **Windows 기능 켜기/끄기**

![config](/assets/img/posts/Etc/2018-09-12/config.png)

## 3. Linux 설치

**Microsoft Store** 에서 '**linux**' 로 검색하면 '**Windows 에서 Linux 실행하기 앱 다운로드**' 라는 항목이 나온다. 이 항목을 클릭하면 설치할 수 있는 Linux 들을 볼 수 있다.

![config](/assets/img/posts/Etc/2018-09-12/search.png)

## 4. Linux 실행 (Ubuntu)

처음 실행하게 되면 계정을 생성하는 부분이 나온다. Windows 계정과 같은 Username 을 사용할 필요는 없으며 Username 과 Password 를 차례대로 입력하면 계정이 생성된다. 생성 후 바로 해당 계정으로 CLI 를 사용할 수 있다. Ubuntu 에서 root 권한으로 명령어를 실행하려면 앞에 `sudo` 를 붙여주면 된다.

![install](/assets/img/posts/Etc/2018-09-12/install.png)

## 5. 유의사항

* Linux 설치는 반드시 **운영체제가 설치된 드라이브**에 설치해야 한다. 설치장소를 변경하려면 '설정 > 시스템 > 저장 공간 > 새 콘텐츠가 저장되는 위치 변경 > 새 앱 저장 위치' 에서 할 수 있다.
* Windows 에서 **Linux 내부 파일**을 생성하거나 수정하면 데이터가 손상되거나 손실 될 수 있다. 더 심하면 Linux 를 다시 설치해야 될 수 있으므로 조심해야 한다.
* Linux 에서 Windows 폴더나 파일을 Mount 하면 접근할 수 있다. 따라서 Windows 에서 개발하고 Linux 에서 테스트할 수 있는 환경을 구축할 수 있다.
* **Docker** 는 설치할 수 없다.

<br>

# 2. 사용하기

## 1. Windows 드라이브에 접근하기

* Windows 드라이브가 이미 Linux 의 '**/mnt**' 폴더에 Mount 되어있다.
* Windows 에 있는 프로젝트 폴더를 간편하게 접근할 수 있도록 **심볼릭 링크**를 만든다.
  * Linux 를 실행하면 기본적으로 접속한 계정의 '**home**' 에서 시작한다.
  * **심볼릭 링크**를 만들면 Linux 안에 프로젝트 폴더가 있는 것처럼 사용할 수 있다.
  * 아래는 계정의 '**home**' 안에 '**dev**' 폴더로 Windows '**e 드라이브**' 에 접근하도록 **심볼릭 링크**를 만든 것이다.

```bash
# ln -s [실제경로] [링크경로]
ln -s /mnt/e /home/lofty87/dev
```

## 2. NPM 오류 해결하기

`npm` 명령어를 사용해보니 아래와 같은 에러를 뿜었다. `which npm` 을 통해 실행파일의 위치도 확인했다. 구글링으로 Windows 에 설치된 `npm` 을 사용해보려고 노력했지만 Windows 와 Linux 둘 다 설치하는 것이 해결책이었다.

```bash
npm -v

# error
: not foundram Files/nodejs/npm: 3: /mnt/c/Program Files/nodejs/npm:
: not foundram Files/nodejs/npm: 5: /mnt/c/Program Files/nodejs/npm:
...
```

* **Package Manager 를 통해 NodeJS 설치**
  * [공식사이트 링크](https://nodejs.org/en/download/package-manager)
  * `curl` 명령어로 NodeJS 를 다운받고 `apt-get` 명령어로 설치한다.

```bash
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -

sudo apt-get install -y nodejs

# 터미널 재시작 (환경변수 적용된 Bash 사용을 위해)
# 'source ~/.profile' 을 실행해 바로 적용을 시도했지만 잘 안됨
```

## 3. Zsh 사용하기

**Zsh(Z Shell)** 을 사용하는 방법은 Bash 와 비슷하다. 하지만 **[Oh My Zsh](https://ohmyz.sh/)** 프레임워크와 결합하면 Bash 보다 더 강력한 기능과 다양한 플러그인을 사용할 수 있다. 특히 탭을 통한 자동완성 기능이 향상됬고 예쁜 [테마](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)들을 터미널에 적용할 수 있어 사용자들에게 인기가 높은 것 같다. 현재 140개가 넘는 테마와 200개가 넘는 플러그인이 있다. **Oh My Zsh** 을 사용하기 위해서는 **Zsh** 버전이 **5.1.1** 이상이어야 하므로 설치하기 전 `zsh --version` 을 통해 **Zsh** 버전을 확인하도록 한다.

* 탭으로 자동완성 기능을 사용하면서 또 탭을 누르게 되면 더 강력한 자동완성 기능을 사용할 수 있다.
* `cd -` 명령어는 히스토리에 쌓인 이전 경로로 이동할 수 있다.
* `cd ../../` 대신 `cd ...`, `cd ../../../` 대신 `cd ....` 같이 더 심플한 명령어로 상위 이동이 가능하다.

```bash
# 1. 사용하고 있는 기본 shell 확인
echo $SHELL

# 2. zsh 설치
sudo apt-get install zsh

# 3. zsh 버전 확인 (5.1.1 이상)
zsh --version

# 4. zsh 을 기본 shell 로 등록
chsh -s $(which zsh)

# 5. 터미널을 재시작하게 되면 Z Shell 설정화면이 나온다.
# (2)번을 눌러 추천 설정이 미리 작성되있는 ~/.zshrc 을 생성한다. (아래 이미지 첨부)

# 6. oh my zsh 설치
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

# 7. 테마 설정 (vi 또는 vim 에디터를 통해 수정할 수 있고 본인이 원하는 테마를 선택)
# ZSH_THEME="agnoster"
vim ~/.zshrc

# 8. 테마 적용
source ~/.zshrc

# 터미널 출력이 이상하다면 다른 폰트로 설정해 본다. (아래 이미지 첨부)
```

* **Z Shell 설정화면**

![zsh-config](/assets/img/posts/Etc/2018-09-12/zsh-config.png)

* **터미널 폰트 수정하기**

![font-config](/assets/img/posts/Etc/2018-09-12/font-config.png)

<br><br>

**※ Reference**
  * [About the Windows Subsystem for Linux](https://docs.microsoft.com/ko-kr/windows/wsl/about)
  * [Install the Windows Subsystem for Linux](https://docs.microsoft.com/ko-kr/windows/wsl/install-win10)
  * [Issue running npm command](https://github.com/Microsoft/WSL/issues/1512)
  * [Resolve npm command issue on Windows Subsystem Linux](https://blur.kr/2018/06/19/Resolve-npm-command-issue-on-WSL)
  * [우분투에 ZSH와 OH MY ZSH 설치하기](http://the-illusionist.me/47)