---
title: spring boot 와 mongo DB 연동
layout: post
categories: backEnd
order: 3
data: 2024-12-17 10:00
---

# spring boot 와 Mongo DB 연동 방법
- [설정](#sm-setting)
- [시작하기](#sm-start)

<p>개발 스펙</p>

- java 21
- maven
- spring boot 3.*
- mongoDB 7.0.14

<h3>
    <a id="sm-setting" target="javascript:void(0)">1. 설정</a>
</h3>

- application.properties 파일에 mongo db 관련 내용을 작성

<a href="/assets/img/backEnd/spring-mongodb-1.PNG" target="_blank"><img src="/assets/img/backEnd/spring-mongodb-1.PNG" /></a>

<p>mongodb 의 기본 port 는 27017 이다.</p>
<p>필자는 mongoDB 를 설치 후 chatdb collection 을 이미 만들었다.</p>
<p>설치 방법이 필요한 경우 <a href="/2024-12/MongoDB#md-download" target="_blank">Mongo DB 사용법</a> 을 참고 바란다.</p>

<br><br>

<h3>
    <a id="sm-start" href="javascript:void(0)">2. 시작하기</a>
</h3>

<p>폴더 구조는 기본 spring project 와 동일하다</p>
<p>Repository 는 mongoDB 를 extends 한다.</p>
<p>mongoDB 의 Repository 는 두 가지다.</p>
<ul>
    <li>
        1. MongoRepository
        <ul><li>기본 spring jpa 와 동일하다.</li></ul>
    </li>
    <li>
        2. ReactiveMongoRepository
        <ul><li>return type 이 reactive 방식이다. (Mono , Flux)</li></ul>
    </li>
</ul>

<p>필자는 Flux 테스트를 위해 2번을 사용했다.</p>
<a href="/assets/img/backEnd/spring-mongodb-2.PNG" target="_blank"><img src="/assets/img/backEnd/spring-mongodb-2.PNG" /></a>

<br />

<p>jpql , queryDsl 처럼 쿼리를 작성 할 수도 있다.</p>
<ul>
    <li>mongoDB 의 Query 어노테이션</li>
    <li>mongoDB 의 MongoTemplate 을 활용</li>
</ul>

<br />

<p>repository 의 메서드는 spring boot 와 동일하며, 특정 쿼리문을 사용 하고싶은 경우 mongoDB 의 Query 어노테이션을 활용하면 된다.</p>
<a href="/assets/img/backEnd/spring-mongodb-3.PNG" target="_blank"><img src="/assets/img/backEnd/spring-mongodb-3.PNG" /></a>

<br />

<p>MongoTemplate 을 활용 하는 방법도 있다. (Aggregation , lookup 등..)</p>
<a href="/assets/img/backEnd/spring-mongodb-4.PNG" target="_blank"><img src="/assets/img/backEnd/spring-mongodb-4.PNG" /></a>

