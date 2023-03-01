---
aliases: []
categories: [조각글, ]
date-created: April 13, 2022 8:02 PM
tags: [Web]
date-updated: 2023-02-27 05:57
---
<aside>
📌 의미는 분명히 나누어지지만 IETF 문서에도 정의가 애매하게 되어있다. 용어가 통일되지 않았다.

</aside>

### Unauthorized(401)

- Authentication 되지 않은 경우.
- **인증** 이 안된 경우
- Invalid_token 문제이다.
- 스프링 시큐리티는 이 경우 기본적으로 302 를 보내어 리다이렉트 시킨다. 이 부분을 바꿔야함.
- AuthenticationEntryPoint

### Forbidden(403)

- 권한 (Authorization) 이 없는경우.
- **인가** 되지 않은 경우.
- Insufficient_scope 문제이다.
- AccessDeniedHandler

[RFC 6750 - The OAuth 2.0 Authorization Framework: Bearer Token Usage](https://datatracker.ietf.org/doc/html/rfc6750)