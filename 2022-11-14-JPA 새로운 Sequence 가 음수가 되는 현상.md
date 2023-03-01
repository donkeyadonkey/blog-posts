---
aliases: []
categories: [조각글]
date-created: November 14, 2022 6:46 PM
Project: Recody
tags: [JPA, 개발 일반]
date-updated: 2023-02-27 06:06
---

### SequenceGenerator 또는 GenericGenerator 의 시작값과 관련되어있다.

- generator 에 명시된 `increment_size` 가 50 인데, 데이터베이스 시퀀스가 1 인경우, -48 에서 시작한다. (-48 에서 1 까지 총 50 개의 시퀀스를 가져와서 그런듯 하다.)
- DB 로부터 시퀀스를 가져오는 양 (예를 들어 50) 이 정해져있는데, 시작값이 그 양보다 작으면 시퀀스의 시작값이 음수가 되는 현상이 발생할 수 있다.
- 이러한 문제는 내가 데이터베이스 시퀀스를 인위적으로 1 로 지정해놓아서 발생한 문제이다.
- 직접 수정하지 않는 이상 큰 문제는 발생하지 않지만, 필요한경우 최소 `increment_size` 이상의 값을 설정하자.