---
layout: post
categories: DB
tags: [ mongodb, mongoose ]
title: "MongoDB 계정관리"
# author: "Lofty87" (default site.author.name)
# author-img: /assets/img/<author>.jpg (default site.author.img)
date: 2018-09-10
---

MongoDB 를 설치한 Local 에서는 언제든지 패스워드 없이 접속할 수 있고 모든 일을 할 수 있다. 이는 외부접속을 허용할 경우 외부 접속자가 허락없이 모든 일을 할 수 있다는 말이다. 보안이 매우 위험한 상태이며 악의적인 의도로 접근할 경우 운영에 심각한 영향을 줄 수 있다. 따라서 MongoDB 를 **Authentication Mode** 로 실행하여 계정들의 접근을 컨트롤할 수 있어야 한다.

* 계정은 각 DB 의 **db.system.users collection** 에 저장된다.
* 계정은 자신의 정보가 저장된 DB 에서만 authenticate 를 할 수 있다.
* 계정이 저장된 DB 에서 authenticate 를 했을 지라도 다른 DB 에 대한 접근권한이 있다면 언제든 그 DB 에 접근이 가능하다. (다른 DB 에 대한 접근권한을 제한하지 않음)

<br>

# 1. Administrator 생성

* admin DB 에 Administrator 를 생성하지 않아도 언제든 다른 DB 에 Administrator 를 생성할 수 있다.
* `root` Role 은 `readWriteAnyDatabase`, `dbAdminAnyDatabase`, `userAdminAnyDatabase`, `clusterAdmin`, `restore`, `backup` Role 을 포함한다.

```js
use admin

db.createUser({
  user: 'root',
  pwd: 'root',
  roles: [{
    role: 'root',
    db: 'admin'
  }]
})
```

<br>

# 2. Authentication Mode

* **Mongo Shell 로 실행하는 경우**

```bash
mongod --auth
```

* **mongod.cfg 로 실행하는 경우**
  1. `security: authorization: enabled` 값을 추가
  2. Service 에 등록하여 실행하고 있는 경우 MongoDB 를 재시작

> security:
<br>
&nbsp;&nbsp;authorization: enabled

```bash
services.msc
```

<br>

# 3. Authenticate

접속할 때 또는 접속한 후 한번만 authenticate 하면 된다.

* **접속할 때**

```bash
mongo -u root -p root --authenticationDatabase admin
```

* **접속한 후**

```js
use admin

db.auth('root', 'root')
```

<br>

# 4. User 생성 및 Authenticate

다른 DB Role 을 추가해 본 것 외에는 위에서 본 Administrator 생성과 동일하다.

* **User 생성**

```js
use test

db.createUser({
  user: 'user',
  pwd: 'user',
  roles: [
    { role: 'readWrite', db: 'test' },
    { role: 'read', db: 'other' } // 다른 DB Role
  ]
})
```

* **Authenticate (접속할 때, 접속한 후)**

```bash
mongo -u user -p user --authenticationDatabase test
```

```js
use test

db.auth('user', 'user')
```

<br>

# 5. User 관련 명령어

* **User 리스트 조회**

```js
show users
```

* **User 조회**

```js
db.getUser('user')
```

* **User 패스워드 변경**

```js
db.changeUserPassword('user', 'newpassword')
```

* **User 삭제**

```js
db.dropUser('user')
```

* **User Role 추가**

```js
db.grantRolesToUser(
  'user', [
    { role: 'readWrite', db: 'test' }
  ]
)
```

* **User Role 취소**

```js
db.revokeRolesFromUser(
  'user', [
    { role: 'readWrite', db: 'test' }
  ]
)
```

<br><br>

**※ Reference**
  * [Mongo Shell Methods](https://docs.mongodb.com/manual/reference/method)
  * [Enable Auth](https://docs.mongodb.com/manual/tutorial/enable-authentication)
  * [Built-In Roles](https://docs.mongodb.com/manual/core/security-built-in-roles)
  * [Configure File Options](https://docs.mongodb.com/manual/reference/configuration-options)