---
layout: post
categories: DB
tags: [ mongodb, mongoose ]
title: "MongoDB 소개"
# author: "Lofty87" (default site.author.name)
# author-img: /assets/img/<author>.jpg (default site.author.img)
description: "MongoDB 는 JSON 으로 이루어진 Document 기반의 NoSQL(Not Only SQL) 이다. 기존의 RDS(Relational Database System) 는 무결성을 지키기 위해 여러 테이블로 나누고 서로 관계를 맺는 구조를 가지는데 반에 NoSQL 은 Key 와 Value 그리고 Collection 이라는 매우 심플한 구조를 가진다. RDS 특성상 Data Sharding 을 하기 위해서는 큰 부담이 따르고 한계가 있기 때문에 Scale Out 보다 Scale Up 을 한다."
date: 2018-09-09
---

# 1. MongoDB 란

[MongoDB](https://www.mongodb.com/) 는 **JSON** 으로 이루어진 Document 기반의 NoSQL(Not Only SQL) 이다. 기존의 RDS(Relational Database System) 는 무결성을 지키기 위해 여러 테이블로 나누고 서로 관계를 맺는 구조를 가지는데 반에 NoSQL 은 Key 와 Value 그리고 Collection 이라는 매우 심플한 구조를 가진다. RDS 특성상 Data Sharding 을 하기 위해서는 큰 부담이 따르고 한계가 있기 때문에 Scale Out 보다 Scale Up 을 한다. Scale Up 은 Scale Out 보다 비용과 시간적인 측면에서 매우 부담이 큰 작업이다. 하지만 NoSQL 은 이런 단점을 극복할 수 있도록 설계되어 있어 Scale Out 에 용이하다. 또 Schema 가 고정되어 있는 RDS 와는 달리 Collection(Table) 안에서 서로 다른 Schema 형태의 Document(Record/Row) 를 가질 수 있어 매우 유연하다. 이런 특징은 Document 안에 Field(Column) 를 확장하거나 축소할 때 편하고 부담이 없다. MongoDB 는 Main Query Language 로 **Javascript** 를 사용하고 있어 Web Developer 에게 친숙하다는 것도 장점이 될 수 있겠다. 하지만 위와 같은 편리함과 유연함은 복잡한 쿼리를 불가능하게 하고 데이터의 무결성을 깰 수 있다는 점에서 단점이 될 수 도 있다.

<br>

|     RDS    |   MongoDB   |
|:----------:|:-----------:|
|  database  |   database  |
|    table   |  collection |
| row/record |   document  |
|   column   |  field/key  |

<br><br>

# 2. 설치하기

## 1. Local (Windows10)

#### 1. [MongoDB Download Center](https://www.mongodb.com/download-center) 에서 Community Server 다운로드

![download](/assets/img/posts/DB/2018-09-09/download.png)

#### 2. Service 에 등록하기

* `mongod` 명령어를 통해 MongoDB 를 직접 실행할 필요 없음 (Windows 부팅시 자동실행)
* Service 에 등록하기 싫다면 **Install MongoD as a Service** 체크해제

![service](/assets/img/posts/DB/2018-09-09/service.png)

#### 3. GUI Client Tool 은 MongoDB Compass 대신 [Robo3T/Robomongo](https://robomongo.org) 를 사용

* Mongo Compass 를 사용해 본 결과 오류도 있고 뭔가 별로라고 생각

![install](/assets/img/posts/DB/2018-09-09/compass.png)

#### 4. db 가 저장될 폴더 생성

* `mongodb` 명령어를 통해 MongoDB 를 직접 실행할 때 인식하는 기본경로

```bash
mkdir -p /data/db
```

#### 5. mongod.cfg

* MongoDB 설치폴더 안에 bin 폴더에 위치
* Service 는 이 **mongod.cfg** 를 참조하여 MongoDB 를 실행 함
* **dbPath 변경**
  * MongoDB 를 설치할 때 Service 등록에서 변경하면 될 것 같은데 설치가 재대로 안되는 문제가 있었음
  * `mongod` 명령어를 통해 직접 실행할 때와 **동일한 storage** 를 사용하기 위해 변경
  * `storage: dbPath: /data/db` 로 변경

※ **.cfg** 나 **.conf** 는 확장자 차이 일 뿐 참조해서 MongoDB 를 실행하고 싶을 때는 선호하는 확장자로 만들면 됨

#### 6. MongoDB 재시작 (mongod.cfg 변경사항 적용)

```bash
services.msc
```

![install](/assets/img/posts/DB/2018-09-09/service-restart.png)

#### 7. 간편한 실행을 위해 bin 폴더를 Path 값에 추가

![path](/assets/img/posts/DB/2018-09-09/path.jpg)

#### 8. README

```text
COMPONENTS

  mongod - The database server.
  mongos - Sharding router.
  mongo  - The database shell (uses interactive javascript).

UTILITIES

  mongodump         - Create a binary dump of the contents of a database.
  mongorestore      - Restore data from the output created by mongodump.
  mongoexport       - Export the contents of a collection to JSON or CSV.
  mongoimport       - Import data from JSON, CSV or TSV.
  mongofiles        - Put, get and delete files from GridFS.
  mongostat         - Show the status of a running mongod/mongos.
  bsondump          - Convert BSON files into human-readable formats.
  mongoreplay       - Traffic capture and replay tool.
  mongotop          - Track time spent reading and writing data.

RUNNING

  For command line options invoke:

    $ ./mongod --help

  To run a single server database:

    $ sudo mkdir -p /data/db
    $ ./mongod
    $
    $ # The mongo javascript shell connects to localhost and test database by default:
    $ ./mongo
    > help
```

## 2. Cloud Service

* **[Atlas](https://www.mongodb.com/cloud/atlas)** : **[Atlas vs mLab](https://www.mongodb.com/blog/post/comparing-cloud-mongodb-services-mongodb-atlas-vs-mlab)**
  * AWS, Azure, GCP
  * Free Tier (M0) : Free / Shared RAM / 512 MB Storage / Shared vCPUs / Limited Regions
* **[mLab](https://mlab.com)** : **[mLab vs Atlas](https://mlab.com/mlab-vs-atlas)**
  * AWS, Azure, GCP
  * Simple Pricing Policy (All-Inclusive Pricing)
  * SANDBOX : Free / Variable RAM / 0.5 GB Storage / Limited Regions

## 3. GUI Client Tool

* **[MongoDB Compass](https://www.mongodb.com/products/compass)**
  * MongoDB 에서 만든 GUI
  * Readonly, Isolated, Community 에디션으로 구분
  * Community 에디션은 무료 (유료 에디션은 없는 것 같음)
* **[Robo3T/Robomongo](https://robomongo.org)**
  * Robomongo 무료 버전
  * 가벼운 GUI
* **[Studio3T](https://studio3t.com)**
  * Robomongo 유료 버전
  * Core, Pro, Enterprise 에디션으로 구분
  * 30일 동안 모든 에디션 사용가능
  * 비상업적인 용도로 사용할 경우 Core 에디션은 항상 무료

<br>

# 3. MongoDB 실행

처음 실행하게 되면 DB 초기 파일들이 생성된다.

* 기본경로 (/data/db)

```bash
mongod
```

* 경로지정

```bash
mongod --dbpath 경로
```

<br>

# 4. MongoDB javascript shell

```bash
mongo
```

<br><br>

**※ Reference**
  * [NoSQL의 장단점](http://lazybrain.ikspres.com/nosql1/)
  * [What query language does MongoDB uses please?](https://www.quora.com/What-query-language-does-MongoDB-uses-please)
  * [SQL to MongoDB Mapping Chart - Terminology and Concepts](https://docs.mongodb.com/manual/reference/sql-comparison/#terminology-and-concepts)