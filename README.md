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

## 3. Front Matter
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

