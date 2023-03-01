---
aliases: []
date-created: March 28, 2022 7:10 PM
tags:
  - Architecture
date-updated: 2023-02-27 05:50
categories:
  - 조각글
---
<br><br>
# 1. 개념 정리
<br><br>
## 왜 Hexagon?

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/33cda12d750b670c030201d3e9eeea82.png)

> 사실 ‘육’각형이어야할 명확한 이유는 없다. 다만, 예상컨대 육각형은 서로 붙여 조립하기 쉬운 형태이기 때문에 선택한 것으로 보인다. 아래에서 설명할 In, Out 의 관계, adapter 의 개수 등이 꼭 숫자 6 에 맞춰질 필요가 없다.

포인트는 외부 의존관계와 어플리케이션 로직이 분리된다는 것이다.
<br><br>
## 요소들

### adapter

> 포트를 통해 인프라와 연결되는 부분의 구현체이다. 크게 두가지로 구분된다.

- Driving Adapter(Primary Adapter) : 사용자의 요청을 받아들이는 Adapter. Inbound Port 와 연결된다고 생각하면 될것 같다.
	- 예를 들어 Controller
- Driven Adapter(Secondary Adapter) : 도메인 모델의 처리에 사용되는 Adapter. Outbound Port 와 연결된다고 생각하면 될 것 같다.
	- 예를 들어 Message Queue, Persistence Adapter(JPA repository 라고 생각하면 될듯)

### port

> 포트는 서비스나 어댑터에 대한 명세를 제공한다. 인터페이스로 정의하고 DI 를 위해 사용된다.
>
> - 네이밍은 Port 로 사용하면 명확해진다.
> - adapter 와의 호환성을 위해 정의하는 인터페이스라고 생각하면 역할이 분명해진다.

> Port 는 application 계층에서만 쓰이는 인터페이스들이므로 패키지 상으로는 application 패키지 안에 위치시키는 것이 일반적인 듯하다.

> 이때 In / Out Bound 는 의존관계를 기준으로 방향을 인지하자.
>
> - repository 와 같은 adapter 의 경우, 데이터를 저장할 수도, 조회할 수도 있다. 그러면 데이터의 방향에 따라 각각 다른 UseCase 를 가지게 되고 Repository 를 사용하는 Port 를 In, Out 각각 정의해줘야 한다.

### application(Service, UseCase)

- 어댑터를 주입받아 도메인 모델과 어댑터를 오케스트레이션 하는 계층이다.
- 핵심은 Adapter 를 주입 받아 Port 를 구현하는 계층이라는 것.
- 서비스 계층의 역할을 한다고 보면 된다.
- 네이밍은 Service, UseCase 등으로 쓰자.

### domain

- 핵심 도메인 모델.
- 원칙적으로 POJO 로 설계하는 것이 좋겠지만 의견에 따라 DB 에 저장될 모습을 갖는 JPA Entity 를 정의할 수도 있다. 만약 POJO 로 설계한다면 JPA Entity 는 Adapter 에 위치하게 된다.

	[Just Stop It! The Domain Model Is Not The Persistence Model](https://blog.sapiensworks.com/post/2012/04/07/Just-Stop-It!-The-Domain-Model-Is-Not-The-Persistence-Model.aspx)

	- 나는 Domain Model 과 Persistence Model 을 분리하는 것에 회의적이다. 왜냐하면 우리가 개발하고있는 애플리케이션은 Spring 프레임워크에 전적으로 의존하는 애플리케이션이기 때문이다. 나는 Spring 으로 개발할 때 Spring 이 사용할 Bean 들이 조화롭게 기능할 수 있도록 내용을 대신 정의해준다는 느낌을 받는다. 따라서 내가 개발하는 애플리케이션은 Spring 에 절대적으로 의존하고 있다고 볼 수 있다. Spring 이 ‘Framework’인 이유가 이와 일맥상통하다고 생각한다. 따라서 헥사고날 아키텍처를 위해서 순수한 도메인을 굳이 별개로 개발할 필요가 없을 것 같다.
	- 정리하자면 Spring Framework 에만 의존하는 domain 을 정의한다고 생각하면 될 것 같다.

	  <aside>
	  😃 약간 생각이 바뀌고 있다.

	  </aside>

	  <aside>
	  📌 아니다 생각이 다시 바뀌고 있다. 22.04.10

	  </aside>
<br><br>
## 패키지 구조

> 아래 패키지 구조를 따랐음

<https://github.com/thombergs/buckpal>

- adapter
	- in
	- out
- application
	- port
		- in
		- out
	- service
- domain
<br><br>
## 참고

- inbound 포트에 대해서는 네이밍을 UseCase 라고 붙여주었다. 의존관계는 안쪽 도메인으로 향하고, Controller 등 외부 Adapter 가 이 UseCase 에 의존한다.(사용한다)
<br><br>
# 2. 적용

[Hexagonal Architecture 리펙토링](https://www.notion.so/Hexagonal-Architecture-56794b42d94b48569ededabb3b6213d7)
<br><br>
# 3. 참고 링크

[헥사고날 아키텍처(Hexagonal Architecture)](https://zkdlu.tistory.com/4)

[Why DDD, Clean Architecture and Hexagonal ?](https://dataportal.kr/74)

[스프링 코드로 이해하는 핵사고날 아키텍처](https://covenant.tistory.com/258)

[Onion vs Clean vs Hexagonal Architecture](https://medium.com/@edamtoft/onion-vs-clean-vs-hexagonal-architecture-9ad94a27da91)

[Clean Coder Blog](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
