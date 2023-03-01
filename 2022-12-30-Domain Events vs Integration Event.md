---
aliases: []
categories: [조각글]
date-created: December 30, 2022 11:32 AM
tags: [Architecture, EDA, 개발 일반]
date-updated: 2023-02-27 06:08
---

[Should you publish Domain Events or Integration Events?](https://youtu.be/53GsiAcKm9k)
<br><br>
## Domain Events

- 같은 서비스 바운더리 안에서.
- 여러 Consumer 가 존재하더라도 하나의 트랜잭션으로 이루어진다.
<br><br>
## Integration Event

- 보통 다른 서비스 바운더리 사이에서, Message Broker 를 통한 메시지 publish, consume
<br><br>
## 어떤 이벤트로 취급할 지에 대해 고려해보기

<aside>
💡 또는 단순히 안/밖의 이벤트라는 개념으로 나눠야 할지?

</aside>

### Stability

> 비즈니스 컨셉 등, 쉽게 바뀌지 않는지에 대한 정도.
> 
- 그렇다면 서비스 내에서 뿐만아니라 Integration Event 로 취급해도 된다.

### Understanding

> 이벤트의 의미가 다른 맥락에서는 전혀 이해할 수 없는 것일 수 있다.
> 
- 한 서비스 바운더리 내에서는 의미가 있지만 다른 서비스에서는 전혀 이해할 수도 없고 의미가 없다면 도메인 이벤트로 취급하는 것이 맞을 것.

### Consumer Requirements

> Consumer 서비스에서 그 이벤트가 필요함.
> 
- Notification, Data Propagation 등.
- Data Propagation 과 같은 경우에는 이벤트 자체로 어떤 맥락에서, 어떤 추가적인 로직이 이루어질지 명확하지 않다.
- 하지만 위와 같은 경우에는 Consumer 측에서 그 이벤트를 받아 로컬 캐시로 저장하는 작업이 이루어 져야 한다.
- 이런 경우 Event Carried Data Transfer.
- 그렇다면 Integration Event 로 고려해야 할 것이다.