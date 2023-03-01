---
aliases: []
date-created: February 5, 2022 5:09 PM
tags:
  - Web
date-updated: 2023-02-27 05:53
categories:
  - 조각글
---
<br><br>
# 0. 들어가기 전에
<br><br>
## 기본적인 HTTP 통신의 특성

- 클라이언트와 서버 간 소통은 단방향으로 이루어진다.
	- 요청은 클라이언트에서만 할 수 있으며 서버에서 요청을 보낼 수 없다.
- 요청과 응답은 1 회성으로 이루어진다.
	- 흔히 ‘stateless’ 하다고 표현하는 것. 통신을 한 후에는 서로 연결은 완전히 종료된다.

### 필요한 기능

- 클라이언트 간 지속적인 데이터 교환
- 클라이언트와 서버 간 지속적인 연결
<br><br>
# 1. 내용
<br><br>
## WebSocket 이전의 기술들

### 1. polling

- 클라이언트가 서버로 지속적으로 요청을 보내 응답을 받아내는 방법이다. 가장 쉽게 생각해낼 수 있는 방법.
- 일정 시간 간격으로 계속 요청을 보낸다.

### 2. Long polling

- 클라이언트가 서버로 지속적으로 요청을 보내는 것은 같다.
- 단, 일정 간격으로 보내는 것이 아니라 time out 될때까지 응답을 기다리는 특성이 있다.
- 따라서 사실상 데이터를 실시간으로 업데이트할 수 있다.

> 단점
>
> - 서버가 응답을 빨리 한다면 너무 많은 요청과 응답이 오가게 되므로 서버에 부담이 된다.

### 3. Streaming

- Polling, Long Polling 과 달리 클라이언트와 서버 간 연결을 끊지않고 필요한 데이터를 계속 전달한다.
- 한 번 연결이 맺어지면 서버가 계속해서 데이터의 상태를 업데이트해 주는 방식이므로 클라이언트가 여러번 요청할 필요가 없다.
<br><br>
## WebSocket

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/0c85c7e91c27ae6033d53a469f06cff4.png)

### 개요

> **HTTP 와 구별되는 전이중 (2-way communication) 통신 프로토콜.**
>
> - 80(HTTP), 443(HTTPS) 포트에서 동작한다. HTTP 와 호환되도록 설계되었다.
> - 웹소켓 프로토콜은 `ws`, `wss` 라는 URI scheme 을 사용한다.

- 참고: URI 구조

	```java
	scheme:[//[user[:password]@]host[:port]][/path][?query][#fragment]
	```

### 방식

> 웹소켓 통신은 다음과 같은 과정을 거친다.
>
> 1. 클라이언트 핸드셰이크 요청
> 2. 서버의 응답으로 연결이 맺어짐
> 3. 데이터 통신

### Handshake

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/6825b836cb37faa01392e8604fb7eb9b.png)

> **핸드 셰이크 과정은 Upgrade HTTP 요청 - 응답으로 이루어진다.**
>
> - 요청 측에서 `‘Connection: Upgrade’` 와 `‘Upgrade: websocket’` 이라는 헤더를 가지고 요청한다.
> 	- 이때 방식은 `GET` 메서드를 사용하며 `HTTP/1.1` 라는 Status Code 를 가진다.
> - 응답 측에서 `‘Connection: Upgrade’`, `‘Upgrade: websocket’` 헤더와 `‘Sec-WebSocket-Accept’` 헤더에 연결을 검증할 키값을 보내 연결이 맺어진다.
>
> <aside>
> 💡 Upgrade 는 일반적 HTTP 연결에서 WebSocket 으로 upgrade 하는 소통이라고 생각하자.
>
> </aside>

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/0f7edd3cd466effde19bf4d7d76de65c.png)

→ 요청 - 응답 헤더에서 `Upgrade` 를 확인할 수 있다.

### 메시지 교환

> 위 핸드셰이크 과정이 성공했다면 그 연결 내에서 메시지를 교환할 수 있다.
>
> - h 는 연결 상태를 확인하는 ping

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/0eb3f2989d615255f47ac652a550a336.png)

### 양방향 통신이라고해서 항상 WebSocket 이 좋은 것은 아니다.

- 변경 사항이 자주 일어나지 않아야 하는 경우,
- 데이터 크기가 작은 경우엔 `polling`, `Long Polling, Streaming` 이 유리할 수 있다.

> 위와 같은 경우 이외에 데이터가 고용량이거나 빈도가 잦은 경우 `WebSocket` 이 좋다.
<br><br>
# 참고 링크

[Spring WebSocket 소개](https://supawer0728.github.io/2018/03/30/spring-websocket/)

[Spring webSocket with stomp 기본 개념 정리](https://wedul.site/692)

[[Spring Boot] WebSocket과 채팅 (1)](https://dev-gorany.tistory.com/212)

[[Spring Boot] WebSocket과 채팅 (2) - SockJS](https://dev-gorany.tistory.com/224)

[[Spring Boot] WebSocket과 채팅 (3) - STOMP](https://dev-gorany.tistory.com/235?category=901854)

[웹소켓 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%EC%9B%B9%EC%86%8C%EC%BC%93)
