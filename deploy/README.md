# Github Page

## 1. Jekyll Install

[Jekyll Document Installation](https://jekyllrb.com/docs/installation)

## 2. 처음 실행 또는 아래와 같이 'Bundler::GemNotFound' 오류가 날 경우

* Windows
  * Could not find public_suffix-3.0.2 in any of the sources (Bundler::GemNotFound)  
  * Could not find gem 'rake (~> 10.0) x64-mingw32' in any of the gem sources listed in your Gemfile. (Bundler::GemNotFound)

* Ubuntu
  * Could not find jekyll-feed-0.10.0 in any of the sources (Bundler::GemNotFound)

```bash
bundle update
```

## 3. Run Server

```bash
jekyll serve
```

## 4. Category 추가

* Category 는 2 Depth 만 사용한다.
  * Post 에서 `categories` 변수는 실제로 <u>multiple</u> 한 Category 설정이지만 index 가 있어 Depth 로 활용
  * Post 에서 `categories` 변수만을 추가하는 것으로 `site` 변수에 Category 를 추가할 수 있다.
* Category Post 들을 보여 줄 수 있는 Category Page 를 생성해야 한다.
  1. Category Page 는 `/page/categories` 에 저장하며 `permalink` 변수 즉, **url** 과 동일하도록 구조가 되있다.
  2. `category.md` 를 추가한다. (기존 파일을 복사해 수정하는 것이 편함)
  3. 1 Depth Category 는 최상위 경로에 두지만 2 Depth Category 는 해당 1 Depth Category 의 폴더 안에 둔다.
* Category Page 에 정의한 변수들은 중복이 있더라도 다 사용되기 때문에 절대 빼먹지 말자!
  * **title** : title 태그 (Pagination 처리된 page 는 suffix 로 'page - N' 이 붙도록 설정했음 [이유있음])
  * **category** : aside 에서 Category 리스트를 뿌릴 때 사용
  * **link** : list 의 Pagination 처리를 위해 사용
  * **permalink** : url 사용자 정의 (폴더구조와 동일)
  * **pagination** : 해당 `category` Post 들만 Pagination 처리

## 5. Post 작성

* 포스트 작성시 '**_post**' 폴더 안에 카테고리에 맞게 발행  
* 파일명은 반드시 '**YYYY-MM-DD-TITLE**' 형태로 저장해야 하며 전부 unique 하게 하는게 좋음  
('**post id**' 값이 같은 값으로 나오는 버그가 있어 정신건강에 해로움)
* 한글이 포함된 '**TITLE**' 은 파일명은 반드시 영어로 하고 Post 변수값은 한글로 작성  
(파일명의 **TITLE** 은 url 값으로 사용되서 문제가 생길 수 있음) 
* site 보다 page 변수값이 우선순위 높음  
(특히 '**author**', '**author-img**' 값을 추가하여 Post 작성자를 변경할 수 있음)

## 6. Front Matter

```txt
---
layout: post  
categories: ES6
tags: [ typescript, es6, commonjs ]
title: "Async Await In ES6"  
author: "Lofty87" (default site.author.name)
author-img: /assets/img/<author>.jpg (default site.author.img)
description: "you can write post description in this line" 
date: 2018-08-13
---
```

## 7. 검색등록을 위한 sitemap, feed, robots.txt 추가

이미 **sitemap.xml** 이나 **feed.xml** 이 있다면 새로 생성하지 않음

### 1. install

```bash
sudo gem install jekyll-sitemap
sudo gem install jekyll-feed
```

### 2. Gemfile

```txt
...
gem 'jekyll-sitemap'
gem 'jekyll-feed'
...
```

### 3. _config.yml

```txt
...
plugins:
  - jekyll-sitemap
  - jekyll-feed
...
```

### 4. 정상적으로 추가되었는지 확인

```txt
http://domain/sitemap.xml
http://domain/feed.xml
```

### 5. robots.txt 추가

```txt
User-agent: *
Allow: /
Sitemap: http://domain/sitemap.xml
```