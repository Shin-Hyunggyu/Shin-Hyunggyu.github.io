---
layout: post
categories: NodeJS
tags: [ nodejs, node-gyp, c++, addon ]
title: "C++ Addon Compile 을 위한 node-gyp"
# author: "Lofty87" (default site.author.name)
# author-img: /assets/img/<author>.jpg (default site.author.img)
description: "'npm' 을 통해 모듈을 설치하다 보면 node-gyp 에러가 종종 발생하곤 한다. 보통 node-gyp 을 사용해야 되는데 설치가 안되어 있거나 또는 node-gyp 을 설치하려고 하는데 설치가 불가능하거나 하는 문제이다. node-gyp 이 무엇인지 알아보기 전에 NodeJS 스펙부터 살펴 볼 필요가 있다. NodeJS 는 CommonJs 표준과 V8 자바스크립트 엔진을 기반으로 만들어진 자바스크립트 런타임이다. 이 V8 자바스크립트 엔진은 C++ 언어로 작성되었으며 자바스크립트 코드를 기계어로 바꾸어 실행하는 역할을 한다. 그렇기 때문에 자바스크립트로 작성하던 모듈들도 C++ 언어로 작성하여 'require' 할 수 있다."
date: 2018-09-14
---

`npm` 을 통해 모듈을 설치하다 보면 [node-gyp](https://github.com/nodejs/node-gyp) 에러가 종종 발생하곤 한다. 보통 node-gyp 을 사용해야 되는데 설치가 안되어 있거나 또는 node-gyp 을 설치하려고 하는데 설치가 불가능하거나 하는 문제이다. node-gyp 이 무엇인지 알아보기 전에 NodeJS 스펙부터 살펴 볼 필요가 있다. NodeJS 는 CommonJs 표준과 V8 자바스크립트 엔진을 기반으로 만들어진 자바스크립트 런타임이다. 이 V8 자바스크립트 엔진은 C++ 언어로 작성되었으며 자바스크립트 코드를 기계어로 바꾸어 실행하는 역할을 한다. 그렇기 때문에 자바스크립트로 작성하던 모듈들도 C++ 언어로 작성하여 `require` 할 수 있다. 스크립트 언어는 생산성이 좋고 편하지만 속도가 느리고 로우한 로직 또는 바이너리 처리가 힘들기 때문에 부분적으로 C++ 언어를 사용하여 모듈을 작성한다면 이런 단점을 보완하고 NodeJS 성능을 향상시킬 수 있다. 이 C++ 언어로 작성된 모듈을 '[C++ Addon](https://nodejs.org/api/addons.html#addons_c_addons)' 또는 'Natvie Node Module' 또는 'Native Addon Module' 이라고 하며 Compile 후 `require` 할 수 있다. 바로 이 Compile 작업을 해주는 도구가 node-gyp 인 것이다. node-gyp 말고도 직접 GCC Compiler 를 사용해 Compile 을 할 수도 있다.

# node-gyp 설치

node-gyp 을 설치하고 사용하려면 Python v2.7 과 C++ 언어를 Compile 할 수 있는 환경이 구축되있어야 한다. Python 버전은 반드시 2.7 이어야 함을 명심하자. Mac OS 라면 Python 이 기본적으로 설치되어 있을 것이고 Xcode 를 통해 나머지 환경만 조성하면 되겠다. 하지만 Windows 는 직접 둘을 설치하고 설정해야만 하는 번거로움이 있는데 정상적으로 설치했다고 해도 node-gyp 이 설치되지 않는 짜증나는 경우가 있다. 이를 해결해 줄 모듈이 바로 [windows-build-tools](https://www.npmjs.com/package/windows-build-tools) 이다. `npm` 을 통해 이 모듈을 설치해주면 자동으로 실행되어 Visual C++ Build Tools (Free Charge) 와 Python 2.7 을 설치해준다. 또 C++ Addon 을 Compile 할 수 있도록 적당한 설정까지 완료해준다. 이미 어떤 환경이 조성되있더라도 충돌하지 않도록 잘 구분해서 설치해주기 때문에 충돌도 걱정할 필요가 없다.

* **1. windows-build-tools**
  * Python 2.7, Visual C++ Build Tools 를 설치해주고 PATH 를 등록해 줌
  * Windows 7 또는 Windows Vista 일 경우 .NET Framework 4.5.1 을 미리 설치해야 함

```bash
// 관리자 권한으로 실행
npm install -g --production windows-build-tools
```

* **2. node-gyp**

```bash
npm install -g node-gyp
```

<br><br>

**※ Reference**
  * [Node.js C++ 모듈 제작 하기](https://blog.naver.com/ojwojwoj/221266536930)
  * [Node.js 용 c++ Addon을 gcc로 컴파일하기 (node-gyp 없이)](http://z-wony.tistory.com/12)