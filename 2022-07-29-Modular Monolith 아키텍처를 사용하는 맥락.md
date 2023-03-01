---
aliases: []
categories: []
date-created: July 29, 2022 9:11 PM
tags: [Architecture]
date-updated: 2023-02-27 05:59
---
<br><br>
# 맥락
<br><br>
## model-code gap(from just enough architecture)

- 우리가 구현하고자하는 소프트웨어의 아키텍처를 코드로 표현하지 못함
- component, layer, 등을 말하지만, 자바에는 그런 키워드가 없다.
- data class 등도 마찬가지. 그걸 클래스로 구현할 뿐임.
<br><br>
## 1. horizontal slicing

- 구성요소로 자르기
- 보통 레이어드 아키텍처의 모습
- web, business, data
<br><br>
## 2. vertical slicing

- Package by feature
- order 패키지에 controller, service, repository 모두 존재

<aside>
💡 어떻게 패키지를 나누느냐는 논쟁적임. 장단이 있다.

</aside>

### Package by feature 의 한계

- 한 기능이 다른 기능을 사용해야하면, 결국은 feature 간 호출이 필요해진다.
- 즉, 결국은 feature 끼리 완전히 분리할 수 없음.
<br><br>
## 3. Ports and adapters

- 메인 강조점: outside depends on inside
- Infrastructure 가 Domain 에 의존한다.
- Domain 은 외부에 의존하지 않는다.
- 실제 기능과는 별 상관없는 adapter 를 구현해야하고, Port 라는 인터페이스를 정의해야하는 상황들이 펼쳐짐.
- 여전히 cargo culted
- **이미 spring framework 가 좋은 추상화를 제공하는데, 더 wrapping 하는 추상화를 할 필요가 있나?**

> 이러한 접근처럼 기능별, 구조별 분리를 하려고해도 결국은 한 feature 가 다른 feature 를 사용하는 상황이 나오게 된다.
> 
- 다른 기능을 사용하려고 public 선언을 해버리면 java 에서는 해당 프로젝트의 모든 패키지에서 접근가능하게 됨.
- 결국은 big ball of mud
- 코드를 쓰다보면 결국은 디자인 원칙 보다는 편의에 따라 의존관계를 만들게 되고, 결국은 스파게티가 됨.
- Architectural principles 가 있어야 한다.
    - contraints, guideline 을 통해서
    - “controller 는 repository 를 직접 접근해서는 안된다.”
        - 어떻게?? 그냥 신경써서?
        - 코드리뷰를 통해서 검증하고??
        - 정말 가능할까?
<br><br>
# 어떻게 해결할까?
<br><br>
## Fitness functions

> from Evolutionary Architectures. 소프트웨어를 개발하면서 발전하는 아키텍처가 기존에 설계했던 모습에 얼마나 적합한지의 정도를 나타내는 함수, 척도.
> 
- 아키텍처를 강제하는 여러 툴이 있다고 함.
    - archjava 등.
- 하지만 이러한 툴에만 의존하면 되는걸까?

- 또, java 의 access modifier 로는 충분하지 않다.
<br><br>
## Package by component

- component 와 관련된 코드들은 모두 한 패키지에 넣자

### Components?

- 관련된 기능 집합
- 어떤 실행 환경 안에 존재한다.

### Software System 이란

- container 들로 구성됨
- container 는 components 를 가짐
- components 는 code element 가 구현한다.
<br><br>
## Impermeable boundaries (방수, 외부로부터 차단하는 경계를 지키기)

- 인터페이스로 component 를 노출시킨다
- 인터페이스를 구현하는 모든 코드는 package-private 으로 통일
- 하지만 우리는 습관대로 public 선언할걸?

### Organisation vs Encapsulation

- **public 선언하는 순간 Encapsulation 과는 거리가 멀어진다.**
- 어떤 아키텍처를 선택하든, 얼마나 신박한 구조를 생각해내든,
    

    public 선언하는 순간, 그 아키텍처의 개념만 표현하고 있을뿐,

    

    소프트웨어 자체는 같은 의미가 된다.(syntactically identical)

    
    - 개념적으로는 다르지만, 문법은 같다.

### 제대로 encapsulation 하자

- 다른 패키지가 의존할 수 없도록.
- public 요소 (자바 인터페이스 등) 는 구현하고자하는 아키텍처에 맞게 정의하자.
    - 즉, compiler 단계에서 미리 검증할 수 있도록.
- 다른 방법으로 module framework 를 사용할 수 있다
    - public, published 로 구분할 수 있다고 함.
- 또다른 방법으로 모듈을 gradle 등으로 아예 나눠버리는 것.
    - source tree 를 나눈다고 표현
    - 멀티 프로젝트 빌드
    - 하지만 빌드 시간도 느려지고 단점들이 있다.

### Agility is a quility attribute

- 마이크로서비스만 고집할 수는 없다. 유행한다고해서.
- 좋은 아키텍처는 agility 를 줌.
    - 높은 modulity 를 가진 것.