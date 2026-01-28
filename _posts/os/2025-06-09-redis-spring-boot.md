---
title: redis spring boot 연결 및 테스트
layout: post
categories: os
order: 16
data: 2025-06-09 13:00
---

# redis spring boot 연결 및 테스트

IntelliJ 기준으로 설명하겠습니다.

## ✅ 1. Spring Boot 프로젝트 생성
▶ IntelliJ에서 새 프로젝트 만들기
1. File → New → Project
2. Spring Initializr 선택 → Next

3. 정보 입력:
- Group: com.example
- Artifact: redis-demo
- Packaging: Jar
- Java: 17 또는 21 (설정된 JDK 기준)

4. Dependencies(의존성) 추가:
- Spring Web
- Spring Data Redis
- Lombok
- (선택) Spring Boot DevTools

→ Finish로 프로젝트 생성

## ✅ 2. Redis 설정 (application.yml 또는 properties)
src/main/resources/application.yml로 아래처럼 설정합니다.
```
spring:
  data:
    redis:
      host: localhost
      port: 6379        # redis 실행중인 port
```

## 3. Redis 연동 코드 예제 (RedisTemplate 이용)
📦 구조
```
com.example.redisdemo
├── config
│   └── RedisConfig.java
├── controller
│   └── UserController.java
├── service
│   └── UserService.java
└── model
    └── User.java
```

### ✅ 3-1. Redis 설정 (RedisConfig.java)
```
package com.example.redisdemo.config;

import com.example.redisdemo.model.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.*;

@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, User> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<String, User> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);

        // Key Serializer
        template.setKeySerializer(new StringRedisSerializer());
        // Value Serializer (User 객체를 JSON 형태로 저장)
        template.setValueSerializer(new Jackson2JsonRedisSerializer<>(User.class));

        return template;
    }
}
```

### ✅ 3-2. User 모델 (User.java)
RedisTemplate은 Serializable 인터페이스를 구현해야 오류 없이 작동합니다.
```
package com.example.redisdemo.model;

import lombok.Getter;
import lombok.Setter;

import java.io.Serializable;

@Getter @Setter
public class User implements Serializable {
    private String id;
    private String name;
    private int age;
}
```

### ✅ 3-3. UserService (UserService.java)
```
package com.example.redisdemo.service;

import com.example.redisdemo.model.User;
import lombok.RequiredArgsConstructor;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
@RequiredArgsConstructor
public class UserService {

    private final RedisTemplate<String, User> redisTemplate;

    private static final String PREFIX = "user:";

    public void saveUser(User user) {
        String key = PREFIX + user.getId();
        redisTemplate.opsForValue().set(key, user, 10, TimeUnit.MINUTES);  // TTL 10분 설정
    }

    public User getUser(String id) {
        return redisTemplate.opsForValue().get(PREFIX + id);
    }

    public void deleteUser(String id) {
        redisTemplate.delete(PREFIX + id);
    }
}
```

### ✅ 3-4. UserController (UserController.java)
```
package com.example.redisdemo.controller;

import com.example.redisdemo.model.User;
import com.example.redisdemo.service.UserService;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/user")
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @PostMapping
    public String saveUser(@RequestBody User user) {
        userService.saveUser(user);
        return "User saved in Redis!";
    }

    @GetMapping("/{id}")
    public User getUser(@PathVariable String id) {
        return userService.getUser(id);
    }

    @DeleteMapping("/{id}")
    public String deleteUser(@PathVariable String id) {
        userService.deleteUser(id);
        return "User deleted from Redis!";
    }
}
```

### ✅ 4, 테스트 방법
Spring Boot 실행 후, Postman 또는 curl로 테스트

➕ 저장
```
# curl -X POST http://localhost:8080/user -H "Content-Type: application/json" -d '{"id":"u1","name":"홍길동","age":25}'
```

🔍 조회
```
# curl curl http://localhost:8080/user/u1
```

❌ 삭제
```
# curl -X DELETE http://localhost:8080/user/u1
```

📌 Redis 사용 예
```
상황          예시                        기술 키워드
캐시          사용자 프로필, 상품 정보	  opsForValue() + TTL
세션 공유      로그인 세션 유지            spring.session.store-type=redis
랭킹 시스템    게시글 좋아요 순            opsForZSet()
인증/유효시간  이메일 인증코드, 임시 링크   set(..., timeout)
동시성 제어    예약 중복 방지, 분산 락	  setIfAbsent (NX 옵션)
```