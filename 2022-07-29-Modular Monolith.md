---
aliases: []
date-created: July 29, 2022 10:03 PM
tags:
  - Architecture
date-updated: 2023-02-27 06:14
categories: [조각글, ]
---

[Understanding the modular monolith and its ideal use cases | TechTarget](https://www.techtarget.com/searchapparchitecture/tip/Understanding-the-modular-monolith-and-its-ideal-use-cases)

[What Is a Modular Monolith? | JRebel by Perforce](http://jrebel.com/blog/what-is-a-modular-monolith)

[Modular Monolith: A Primer - Kamil Grzybek](http://kamilgrzybek.com/design/modular-monolith-primer/)

<https://github.com/kgrzybek/modular-monolith-with-ddd>

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/40d7d46f4d8548c5ee05cd30b9ff9a9a.png)

<br><br>
# Monolith

- 배포 단위가 1 개인 아키텍처
- 가장 일반적인 형태.
<br><br>
# MSA

- 별개의 서비스 바운더리를 가지는 여러 애플리케이션들의 조합.
- 구분 상 별도의 version controll, 별개의 repo, 별개의 배포 주기를 가짐.
<br><br>
# Modular Monolith

- 배포 단위는 여전히 1 개이지만 bounded context 를 모듈로 나누어 구현하는 아키텍처
- 바운더리 내에서는 바운더리 외부와 격리된 도메인을 가지도록 설계한다.
- 각 서비스 바운더리는 각자의 public API 를 노출하여 소통하도록 한다.
<br><br>
## 장점

- 기능이 명확하게 나뉜다.
- MSA 보다 덜 복잡하다.
- MSA 보다 개발, 유지보수 및 배포가 쉽고 빠르다.
- 모듈 간 의존관계를 명확하게 나눌 수 있다.
<br><br>
## 단점

- 코드 양이 모노리스보다 많아진다.
- 모노리스인 이상 배포에 실패할 경우 전체 서비스의 다운을 의미한다.
- [수직적 스케일링](https://www.cloudzero.com/blog/horizontal-vs-vertical-scaling) 이 불가능.
<br><br>
# 서비스 바운더리는 어떻게 나누는가?

- 설계에 따라 매우 달라지는 부분.
- 도메인을 공유하는 부분이 최소가 되도록 정해야 한다.
- 기능에 따라 어떻게 나누는지 여러 사례들을 찾아보고 정할 필요가 있다.
- 지나치게 쪼개면 그만큼 코드양이 많아질 것이고 관리가 어렵게 된다.
<br><br>
# Monolith 어떻게 분리할까?

> Monolith 분리의 단계
<br><br>
## 코드 분리

1. Separate Package
		- 하나의 Jar
		- 같은 version control
		- 하나의 app
2. Maven Module(or gradle)
		- 여러 Jar
		- 같은 version control
		- 하나의 app
3. Separate repo
		- 여러 Jar
		- 각각 version control
		- 하나의 app
4. External Service → Microservices
		- 여러 Jar
		- 각각 version control
		- 여러 app
<br><br>
## Database 분리

1. Separate Table

- same schema
- same database
- same RDBMS (db 인스턴스를 말함)

2. Separate Schema

> mysql 은 schema 와 database 를 같은 것으로 취급함.

- different schema
- same database
- same RDBMS

3. Separate Database

- different schema
- different database
- same RDBMS

4. Other Persistence

- different schema
- different database
- different (R)DBMS
<br><br>
## 보안 관련 사항

Modular Monolith 는 바운더리 내부에서 독립적으로 동작하기 때문에 보안 측면에서는 다른 아키텍처들과 비교해 볼 필요가 있다.

먼저 모듈들간의 보안 인증을 위해 모듈들을 분리하는 것을 고려해야 한다. 이는 모듈들이 분리된 상태로 운영될 수 있도록 하고, 모듈들의 외부 접근을 제한하며, 모듈들간의 인증을 할 수 있도록 한다. 이를 위해서는 여러 라이브러리들이 있는데, 이 중 하나로는 [JWT](https://jwt.io/) 라이브러리를 사용할 수 있다. JWT 는 메시지 간 인증을 위해 모듈들간 JSON Web Tokens 을 사용하여 메시지를 인증하는 방법이다.

또한 모듈들간의 메시지 교환에 대한 보안 관련 연구도 필요하다. 모듈들간의 메시지 교환은 모듈들이 독립적으로 동작하는 것을 가능하게 하기 때문에, 이 메시지 교환들이 보안에 취약하지 않게 하는 것이 중요하다. 이를 위해서는 [OWASP](https://owasp.org/www-project-web-security-testing-guide/) 같은 라이브러리를 사용할 수 있다. OWASP 는 웹 어플리케이션 보안 테스트를 위한 가이드 라이브러리 이다.

또한 보안 보호 정책과 관련된 연구를 위해 특정 모듈의 사용을 제한하고, 모듈들간의 접근 방식을 제어하는 방법을 고려해야 한다. 또한 모듈들간 데이터 암호화를 통해 데이터 송수신의 보안을 유지할 수 있는 방법도 고려되어야 한다.
