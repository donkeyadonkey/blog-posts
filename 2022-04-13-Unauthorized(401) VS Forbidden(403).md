---
aliases: []
categories: [์กฐ๊ฐ๊ธ, ]
date-created: April 13, 2022 8:02 PM
tags: [Web]
date-updated: 2023-02-27 05:57
---
<aside>
๐ ์๋ฏธ๋ ๋ถ๋ชํ ๋๋์ด์ง์ง๋ง IETF ๋ฌธ์์๋ ์ ์๊ฐ ์ ๋งคํ๊ฒ ๋์ด์๋ค. ์ฉ์ด๊ฐ ํต์ผ๋์ง ์์๋ค.

</aside>

### Unauthorized(401)

- Authentication ๋์ง ์์ ๊ฒฝ์ฐ.
- **์ธ์ฆ** ์ด ์๋ ๊ฒฝ์ฐ
- Invalid_token ๋ฌธ์ ์ด๋ค.
- ์คํ๋ง ์ํ๋ฆฌํฐ๋ ์ด ๊ฒฝ์ฐ ๊ธฐ๋ณธ์ ์ผ๋ก 302 ๋ฅผ ๋ณด๋ด์ด ๋ฆฌ๋ค์ด๋ ํธ ์ํจ๋ค. ์ด ๋ถ๋ถ์ ๋ฐ๊ฟ์ผํจ.
- AuthenticationEntryPoint

### Forbidden(403)

- ๊ถํ (Authorization) ์ด ์๋๊ฒฝ์ฐ.
- **์ธ๊ฐ** ๋์ง ์์ ๊ฒฝ์ฐ.
- Insufficient_scope ๋ฌธ์ ์ด๋ค.
- AccessDeniedHandler

[RFC 6750 - The OAuth 2.0 Authorization Framework: Bearer Token Usage](https://datatracker.ietf.org/doc/html/rfc6750)