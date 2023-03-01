---
aliases: []
date-created: March 18, 2022 1:42 PM
tags:
  - Infra
date-updated: 2023-02-27 05:53
categories:
  - 조각글
---

[메시지 큐에 대해 알아보자!](https://tecoble.techcourse.co.kr/post/2021-09-19-message-queue/)
<br><br>
# 간단 정리

클라이언트 - 서버 구조에서 통신하는 메시지를 잠시 저장하는 저장소
<br><br>
## 사용하는 경우

- 아주 즉각적인 처리가 필요하지 않은 경우.
<br><br>
## 이점

- 비동기적

	: 메시지 저장, 전송에 대해 동기화 처리를 진행하지 않고 큐에 넣어둠.

- 낮은 결합도

- 확장성

- 탄력성

- 보장성

### 이 채팅 애플리케이션에서 Message Queue 를 사용해야하는 이유?

- 사용자가 많아질 경우 채팅 데이터를 처리하는 SimpleBroker 는 채팅 서버의 메모리를 많이 차지하게 된다. 그러면 다른 비즈니스 로직도 있다면 하나의 서버가 많은 부담을 안게 된다. 따라서 채팅이 서버에 가하는 부하를 줄이고자 Message Queue 라는 미들웨어를 두는 것.
<br><br>
# 디펜던시 세팅하기

### docker 활용 MQ

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/ed55b87dcdf74075cbdac7e11ee6648b.png)

> RabbitMQ docker 이미지의 기본 User, Password
>
> - guest / guest

> 바꾸려면 환경변수를 추가한다.

```yaml
...
		environment:
		      RABBITMQ_DEFAULT_USER: 이름
		      RABBITMQ_DEFAULT_PASS: 비밀번호
...
```

[Rabbitmq - Official Image | Docker Hub](https://hub.docker.com/_/rabbitmq)
<br><br>
# RabbitMQ 기본적 이해
<br><br>
## Producer — Queue — Consumer

> producer → Queue → Consumer 의 관계를 가지는 것을 고려하여 내용을 이해해보자.
<br><br>
## 연결 (java client 기준)

### 과정

> 메시지 브로커와 Producer, Consumer 가 연결되는 과정이다.
>
> - ConnectionFactory 를 이용해 Connection 을 만든다.
> - Connection 을 이용해 Channel 을 만든다.
> - channel 에 Queue 를 선언한다.
> - Queue 에 메시지를 publish 하거나 Queue 에서 메시지를 recieve(consume) 한다.

### Connection

> 어플리케이션과 RabbitMQ broker 간의 TCP 연결이다.
>
> - host, port, username, password 등을 세팅해줄 수 있다.

- doc
<br><br>
## **[Connections](https://www.rabbitmq.com/tutorials/amqp-concepts.html#amqp-connections)**

> 	**AMQP 0-9-1 connections are typically long-lived. AMQP 0-9-1 is an application level protocol that uses TCP for reliable delivery. Connections use authentication and can be protected using TLS. When an application no longer needs to be connected to the server, it should gracefully close its AMQP 0-9-1 connection instead of abruptly closing the underlying TCP connection.**
> 

### Channel

> Connection 속에 존재하는 가상의 연결. message 를 publish 하거나 consume 하는 것은 Channel 상에서 이루어진다.

- doc
<br><br>
## **[Channels](https://www.rabbitmq.com/tutorials/amqp-concepts.html#amqp-channels)**

> 	**Some applications need multiple connections to the broker. However, it is undesirable to keep many TCP connections open at the same time because doing so consumes system resources and makes it more difficult to configure firewalls. AMQP 0-9-1 connections are multiplexed with _[channels](https://www.rabbitmq.com/channels.html)_ that can be thought of as "lightweight connections that share a single TCP connection".**

> 	**Every protocol operation performed by a client happens on a channel. Communication on a particular channel is completely separate from communication on another channel, therefore every protocol method also carries a channel ID (a.k.a. channel number), an integer that both the broker and clients use to figure out which channel the method is for.**

> 	**A channel only exists in the context of a connection and never on its own. When a connection is closed, so are all channels on it.**

> 	**For applications that use multiple threads/processes for processing, it is very common to open a new channel per thread/process and not share channels between them.**

### exchange

> 큐에 메시지를 전달하기 위한 정거장.
>
> - 문서에서는 공항으로 비유되고 있다.

> 4 가지 타입이 존재한다.
>
> - Direct Exchange
> - Fanout Exchange
> - Topic Exchange
> - Headers Exchange

### Bindings

> exchange 가 message 를 queue 로 routing 하는 데에 사용하는 규칙.
>
> > 어떤 메시지를 어떤 큐에 전달할 것인가?
>
> - routing key 라는 속성을 가질 수 있다. 이 key 에 따라 큐에 전달할 메시지가 선택된다.
> - 즉, 필터같은 역할을 함.
<br><br>
# Spring 애플리케이션에 적용
<br><br>
## Configuration

> 몇가지 규칙이 있다.
>
> - Queue 를 생성
> - Exchange 를 생성
> - Binding 빈 생성: Queue 와 Exchange 를 어떤 패턴으로 바인딩한다.

### ConnectionFactory

`com.rabbitmq.client.ConnectionFactory;` 가 아니라

`org.springframework.amqp.rabbit.connection.ConnectionFactory` 를 사용할 것.

### RabbitTemplate

> 아래 객체들을 세팅할 수 있다.
>
> - ConnectionFactory
> - MessageConverter
> - String routingKey: 이 routingKey 로 들어오는 메시지에 대해서 Queue 와 Exchange 를 바인딩 한다.

### SimpleMessageListenerContainer

> ConnectionFactory 와 QueueName 들을 갖는다.

### MessageListener

> Queue 이름으로 들어오는 메시지를 받는다.

1. 설정에서 Container 에 등록할 수 있다.
		- 직접 구현해서 등록한다.
		- Config 에서 `container.set...`
		- 불편한듯.
2. 어노테이션으로 등록할 수 있다.
		- `@RabbitListener`
		- 스프링의 Controller 에 등록해두면 자연스럽다.

[Spring Boot와 RabbitMQ 초간단 설명서](https://velog.io/@hellozin/Spring-Boot%EC%99%80-RabbitMQ-%EC%B4%88%EA%B0%84%EB%8B%A8-%EC%84%A4%EB%AA%85%EC%84%9C)

[[AMQP][RabbitMQ]RabbitMQ아주 기초적이게 사용하기 - Java(feat.Hello World!) - (2)](https://kamang-it.tistory.com/entry/AMQPRabbitMQRabbitMQ%EC%95%84%EC%A3%BC-%EA%B8%B0%EC%B4%88%EC%A0%81%EC%9D%B4%EA%B2%8C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-JavafeatHello-World-2)

[RabbitMQ tutorial - "Hello World!" - RabbitMQ](https://www.rabbitmq.com/tutorials/tutorial-one-java.html)

[AMQP 0-9-1 Model Explained](https://www.rabbitmq.com/tutorials/amqp-concepts.html)

> RabbitMQ 서버가 docker 를 통해 올라가있으므로 docker 컨테이너의 61613 포트를 꼭 열어주자.
>
> - 61613 은 stomp client 를 사용해 접속하는 경우 사용하는 포트라고 한다.
>
> [RabbitMQ](https://coe.gitbook.io/guide/messaging/rabbitmq)
>
> ```yaml
> ...
> 	rabbitmq:
> 	    container_name: cuppa-mq
> 	    image: rabbitmq:management
> 	    environment:
> 	      RABBITMQ_DEFAULT_USER: cuppamq
> 	      RABBITMQ_DEFAULT_PASS: cuppaadmin
> 	    ports:
> 	      - "5672:5672"
> 	      - "15672:15672"
> 	      - "61613:61613"
> ...
> ```
>

> → ※ 클라이언트에서 연결이 안되는 문제를 해결
>
> [클라이언트에서 StompClient를 이용하여 RabbitMQ 에 연결할 수 없는 문제](https://www.notion.so/StompClient-RabbitMQ-5d1e18d73b12401083b88f71942fec04)

> 메시지를 받는 쪽에서 MessageMQ 를 거쳐서 온 결과물
>
> - 여러가지 헤더가 많아졌다. 줄이는 게 좋아보이지만 무엇이 필요한지는 차차 알아보는 걸로.
>
> ![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/d88cc680b533608b4483710c391c536d.png)

### Spring 에서 RabbitMQ 사용하기

[Messaging with RabbitMQ](https://spring.io/guides/gs/messaging-rabbitmq/)
<br><br>
# 다음 할일

[virtual-host 로 개발 환경을 분리한 메시지 큐 사용하기](https://www.notion.so/virtual-host-a55e7a513ebe4ef684ba03d34d7d76e0)

- 클라이언트에서 메시지를 보낼 때 Stomp Broker 에 login 값과 passcode 값을 넘기도록 하는 게 맞을까?
