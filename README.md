# Dev Store Blog

## 1. Run Server
```yaml
jekyll serve
```

## 2. 아래와 같은 오류가 날 경우
- Could not find public_suffix-3.0.2 in any of the sources (Bundler::GemNotFound)  
- Could not find gem 'rake (~> 10.0) x64-mingw32' in any of the gem sources listed in your Gemfile. (Bundler::GemNotFound)

```yaml
bundle update
```

## 3. Category
- 카테고리를 추가하기 위해서는 '**page**' 폴더에 기존 카테고리 파일을 복사하고 수정만 하면 됨  
(Post 작성시 '**categories**' 변수만을 추가하는 것으로 site 에 카테고리를 추가할 수 있음)
- 카테고리는 1Depth 만 사용

## 4. Post 작성
- 포스트 작성시 '**_post**' 폴더 안에 카테고리에 맞게 발행  
- 파일명은 반드시 '**YYYY-MM-DD-TITLE**' 형태로 저장해야 하며 전부 unique 하게 하는게 좋음  
('**post id**' 값이 같은 값으로 나오는 버그가 있어 정신건강에 해로움)
- 한글이 포함된 '**TITLE**' 은 파일명은 반드시 영어로 하고 Post 변수값은 한글로 작성  
(파일명의 **TITLE** 은 url 값으로 사용되서 문제가 생길 수 있음) 
- site 보다 page 변수값이 우선순위 높음  
(특히 '**author**', '**author-img**' 값을 추가하여 Post 작성자를 변경할 수 있음)

## 5. Front Matter
```txt
---
layout: post  
categories: ES6
title: "Async Await In ES6"  
author: "Lofty87" (default site.author.name)
author-img: /assets/img/<author>.jpg (default site.author.img)
date: 2018-08-13
---
```