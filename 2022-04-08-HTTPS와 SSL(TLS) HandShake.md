---
aliases: []
categories: [조각글]
date-created: April 8, 2022 3:19 PM
tags:
  - Web
date-updated: 2023-02-27 05:54
---
<br><br>
# HTTPS

- “S”: Over Secure Socket Layer
- 도메인에 대한 소유권을 인증함.
- 데이터가 암호화됨
- 제 3 자에 의해 변조, 감청될 수 없도록 함.
<br><br>
## SSL(TLS)

- TCP 통신에서 데이터를 암호화하는 프로토콜.
- HTTP 가 SSL 을 사용하면 HTTPS 이다. (HTTP over TLS)

### SSL 디지털 인증서

- 클라이언트와 서버간 통신을 제 3 자가 보증해주는 전자화된 문서
- 접속하려는 웹 서버가 진짜 그 서버가 맞는가? 를 보증
- 통신 내용이 공격자에게 노출되는 것을 막을 수 있다.
	- 통신 내용을 암호화 한다. → 암호화
	- 또, 통신 내용을 받는 쪽은 그 내용을 이해할 수 있도록 해줌. → 복호화
	- 복호화 하기 위해서는 Key 가 필요하다.

[2022-04-09-Open SSL 설치](2022-04-09-Open%20SSL%20설치.md)
<br><br>
# 암호화 방식
<br><br>
## 1. 대칭키 방식

> 이 대칭키를 가지고 있으면 어느쪽이든 정보를 복호화할 수 있다.

아무 텍스트 파일을 암호화 해보자

```bash
openssl enc -e -des3 -salt -in randtext.txt -out ciphertext.bin;
```

```bash
openssl enc -d -des3 -in ciphertext.bin -out randtext2.txt;
```

- -e 는 암호화, -d 는 복호화 한다는 뜻.
- des3 라는 암호화방식을 사용하고
- in, out 을 설정해준다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/1d28a716ba5c15461c44e306b8e9ec3f.png)

### 결론

→ 혼자 사용하면 상관없다. 하지만 누군가에게 전달하려면 결국 key 값을 줘야하고, 그 key 를 다른데에다 뿌려버리면 아무나 복호화할 수 있게 된다.
<br><br>
## 2. 공개키 (public key) 방식

> public key 로 암호화 + private key 로 복호화
>
> 어느 한쪽만 정보를 복호화 할 수 있다. 비대칭이됨.

- 두개의 키를 사용한다.
- A 키로 암호화 했다면 B 라는 키로만 복호화를 할 수 있다.
- 이때 A 는 공개키 (public key), B 는 비밀키 (private key, secret key)

### 비밀키로 암호화 하기?

> 특정 대상 (예를 들어 CA) 이 암호화한 정보라는 것을 확인시키기 위해 의도적으로 비밀키를 이용해 암호화하기도 한다.

- 원칙상 공개키로 정보를 암호화 한다. 하지만 비공개키로 암호화를 할 수도 있다. 그러면 공개키를 이용해서 이 정보를 복호화 할 수 있게 된다. 그러면 정보가 쉽게 노출 될 수 있어 보인다. 하지만 이것은 정보를 암호화하는 것이 목적이 아니다. 어떤 공개키로 복호화 할 수 있다는 것의 의미는 곧 이에 상응하는 비밀키로 암호화 되었다는 것을 의미한다. 이와 같이 특정 대상이 암호화 했다는 것을 증명하기 위해 비밀키로 정보를 암호화 할 수도 있다.
- 이는 CA 가 SSL 인증서를 비밀키를 이용해 암호화 한 뒤 어떤 대상이 CA 가 공개한 공개키를 사용해 복호화 함으로써 이 인증서가 그 CA 에 의해 암호화된 인증서라는 것을 증명 할 수 있게 된다.
- 어떤 Key 로 암호화 한다는 것은 곧, 그 Key 를 사용했다는 서명 했다는 것을 의미하게 되는 듯 하다.

### 과정

- private key 생성

	```bash
	openssl genrsa -out private.pem 1024;
	```

- private key 를 가지고 public key 를 생성

	```bash
	openssl rsa -in private.pem -out public.pem -outform PEM -pubout;
	```

- public key 로 파일을 암호화

	```bash
	openssl rsautl -encrypt -inkey public.pem -pubin -in file.txt -out file.ssl;
	```

- private key 로 파일을 복호화

	```bash
	openssl rsautl -decrypt -inkey private.pem -in file.ssl -out decrypted.txt
	```
<br><br>
# SSL 인증서

- 클라이언트가 접속한 서버가 의도한 그 서버인가?
- SSL 통신에 사용할 서버의 공개키를 클라이언트에게 제공
<br><br>
## CA

Certificate authority, root Certificate

- 어떤 사이트가 신뢰할 수 있는 사이트인지 보증하는 기관
- 이러한 기업들은 매우 엄격하게 선정된다.
- 브라우저는 안전한지 확인하는 CA 의 리스트를 가지고 있다.
- 이러한 업체에게 비용을 지불하고 인증서를 구입해서 사용하면 된다.
- 도메인을 구매했으면 그 도메인에 대한 소유권을 증명할 수 있음.
- 브라우저가 모르는 사설 인증서를 사용하면 HTTPS 를 사용해도 브라우저에서 안전하지 않다는 표시를 할 수도 있다.
<br><br>
## SSL 인증서의 내용

1. 서비스의 정보 ( CA 가 어디인지, 서비스의 도메인...)
2. 서버측 공개키

> 여기서부터 클라이언트는 브라우저의 행동이라고 생각하면 된다.

### 서버가 SSL 인증서를 발급받기 위해

1. 서버의 정보를 CA 에 전달

		서버의 정보란: 서버의 공개키, 각종 서버 정보

2. CA 에서 이 정보를 담은 SSL 인증서를 발급

3. SSL 인증서의 내용을 암호화하기 위해 공개키, 비밀키를 생성

4. 비밀키로 인증서를 암호화 (CA 의 서명이라고 보면 될 듯.)
		- 여기서 CA 의 공개키를 이용해야만 이 인증서를 복호화 할 수 있게 된다.

5. 암호화된 인증서를 서버로 전송 (인증서가 발급됨)

### 브라우저가 특정 서버가 신뢰할 수 있는 서버인지 확인하는 과정

1. 클라이언트가 브라우저를 사용해 특정 서버에 접속을 시도한다.
2. 서버는 브라우저에게 SSL 인증서를 보낸다.
3. 브라우저는 SSL 인증서를 여러 CA 의 공개키를 사용해 인증서를 복호화 한다.
4. 특정 CA 의 공개키를 사용해 복호화에 성공한다면 그것은 그 CA 에 의해 발급된 인증서라는 뜻이다.
5. 따라서 브라우저는 이 서버에 대해 신뢰할 수 있는 사이트라는 표시를 하게 된다.

> CA 가 비밀키를 유출하지 않는 것이 중요한 이유이다.
>
> - 만약 CA 가 비밀키를 유출하게 되면 브라우저 입장에서는 특정 SSL 인증서가 CA 가 암호화한 인증서가 맞는지 확신할 수 없게 된다.
<br><br>
## 서비스 (서버) 의 공개키는 어떤 역할?

- 데이터를 전송할 때 암호화하기 위해 공개키 방식을 사용하면 높은 수준의 보안을 할 수 있지만 많은 컴퓨팅 파워를 요구한다. 매번 정보를 전달하기 위해 공개키 방식으로 암호화하는 것은 경제적이지 못하다.
- 따라서 정보를 암호화하는 것은 대칭키 방식을 사용한다.
- 단, 이 대칭키를 암호화하기 위해 공개키 방식을 사용한다.
<br><br>
# HTTPS 통신을 위한 TLS(SSL) Handshake

> 서버와 클라이언트가 암호화된 통신을 하는 과정. TCP/IP 네트워크를 사용하는 통신에 적용된다.
<br><br>
## 1. 악수 (handshake)

> 핸드셰이크는 여러가지 방식이 있고, 대표적으로 RSA 방식, Diffie-Hellman 방식이 있다고 한다.
>
> 아래는 RSA 방식이다.

> 악수의 과정은 결과적으로 암호화된 데이터 통신을 위해 대칭적인 Session Key 를 만드는 과정이다.
>
> - 서버와 클라이언트가 외부에 노출되지않고 각자가 같은 Session Key 가지기 위해 아래와 같은 과정을 거치는 것이다.
> - 대칭적인 Session Key 를 만들기 위해 비대칭적 암호화 방법 (공개 - 비밀키 방식) 를 사용한다고 이해하자.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/0c8452265ea781fa01b487dd7f6e3adc.jpeg)

### 1. Client Hello 에서 전달하는 데이터

- 클라이언트 측의 랜덤 데이터 (_**Client Random**_)
	- Master Secret 을 생성하는 데에 사용한다.
- 클라이언트가 지원하는 암호화 방식
	- 어떤 암호화 알고리즘을 사용할 수 있는가?
- 세션 ID:
	- 핸드 쉐이크에 대한 정보가 담겨있는 세션 ID 이다.
	- 서버는 이 세션을 확인하여 핸드쉐이크 한 기록이 있다면 기존 세션을 활용한다.
		- 세션이 있다면 복잡하게 Session Key 를 계산하는 과정이 필요 없겠지.

### 2. Server Hello 에서 전달하는 데이터

- 서버 측의 랜덤 데이터 (_**Server Random**_)
	- Master Secret 을 생성하는 데에 사용한다.
- 클라이언트가 지원하는 방식 중 서버가 선택한 암호화 방식 (_**Cipher Suite**_ 라고 하는 듯)
- 인증서

### 3. 서버가 전달한 데이터를 확인

- 서버가 보낸 인증서를 먼저 확인한다.
	- 클라이언트가 알고있는 CA 에 의해 인증되었는지 여러 CA 의 공개키로 복호화 함으로써 확인
	- 이 인증서를 복호화 하는 데에 성공하면 **서버측이 보낸 공개키** 를 얻을 수 있게 된다.
- 랜덤값으로 Premaster Secret 을 생성 & 암호화
	- Master Secret 을 계산하는 데에 사용한다.
	- 서버의 공개키를 사용해 Premaster Secret 을 암호화 한다.
- 암호화된 Premaster Secret 을 서버로 전송 (**ClientKeyExchange**)

### 4. 클라이언트에서 보낸 데이터를 서버가 확인

- 서버가 가진 비밀키를 사용해 Premaster Secret 을 복호화
	- 여기서 서버와 클라이언트 모두가 Premaster Secret 을 가지고 있게 된다.
- Premaster Secret 을 사용하여 Master Secret 를 생성한다.
- Master Secret 을 사용해 Session Key 를 생성한다.
- 이 Session Key 를 사용해 데이터를 암호화하여 주고받는다.
- “*Secret”은 그때 그때 랜덤한 값을 만들기 위해 생성하는 것이고, “*Key”는 실제 데이터를 암호화 하는데에 쓰기 위해 만드는 것이라고 이해하자.

### 5. 각자가 Session Key 를 만든다.

- Premaster Secret, Client Random, Server Random 을 사용해 Master Secret 을 만들고 Session Key 를 생성한다.
	- 암/복호화에 사용될 Session Key 가 외부에 노출되는 과정 없이 각각이 같은 Session Key 를 가지게 되었다.
- 각자의 Session Key 를 가지고 “finished”라는 메시지를 암호화하여 보낸다.(_**ChangeCipherSpec**_ 메시지를 보낸다고 표현)
- 서로의 Session Key 를 가지고 데이터를 암/복호화 수행.
- 결과적으로 이 Session Key 가 대칭키의 역할을 수행한다.

### Master Secret?

- 48 bytes 의 고정된 길이를 가지고 있다.

- 유동적인 크기를 가진 Premaster Secret 을 일련의 계산법을 사용하여 Master Secret 을 계산할 수 있다.

	[RFC 5246 - The Transport Layer Security (TLS) Protocol Version 1.2](https://datatracker.ietf.org/doc/html/rfc5246#section-8.1)

- 결론: `(Premaster Secret, Client Random, Server Random) => Master Secret`

- 이 Master Secret 을 이용하여 Session Key 를 만드는데, 이는 통신에 필요한 여러가지 Key 를 지칭한다고 한다.

	[Differences between the terms "pre-master secret", "master secret", "private key", and "shared secret"?](https://crypto.stackexchange.com/questions/27131/differences-between-the-terms-pre-master-secret-master-secret-private-key)

### 한계

> 서버측 Private Key 를 탈취당하면 Session Key 를 유추할 수 있다.
>
> - RSA 방식을 사용했을 때의 얘기이다.
<br><br>
## 2. 세션

> 생성한 Session Key 를 가지고 암호화하여 데이터를 보내고, 복호화하여 데이터를 확인한다.
>
> 이 세션에서 HTTPS 통신이 가능한 것이다.
<br><br>
## 3. 세션 종료

> 데이터 전송이 끝나면 서로에게 통신이 끝났음을 알린다. 그리고 Session Key 를 폐기한다.
>
> - 따라서 이 Session Key 를 누군가 탈취한다고 하더라도 세션이 끝나면 데이터를 볼 수 없다.
> - Session Key 를 탈취하거나, 서버측 Private Key 를 탈취하더라도 이미 데이터 통신이 끝났다면 아무 의미가 없게 된다.

### 세션은 언제부터 언제까지를 말하는가?

> 세션 ID 로 식별되는 같은 Master Secret 을 사용한 연결들을 말한다.
>
> - Handshake 를 요약하자면 클라이언트와 서버가 소통하기 위해 비대칭 암호화 전략을 사용하여 대칭 키를 도출 (_derive_ 라고 보통 표현) 하는 과정을 의미한다.
>
> - Handshake 는 각자의 Random 값을 교환하여 Master Secret 을 도출하는 “full handshake”가 있고, Session ID 를 사용하여 이전의 연결에서 사용한 Master Secret 을 재사용하는 데에 합의하는 “abbreviated handshake” 가 있다.
>
> 	  <aside>
> 	  📌 *abbreviated handshake 를 하는데에 각자가 합의한다면, Session Key 는 갱신된다. 다만 Master Secret 은 같은 것을 쓰는 것이다.*
>
> 	  </aside>
>
> - 어떤 handshake 를 하든 같은 Master Secret 을 사용하는 소통이면 하나의 세션으로 본다.
>
> - 이는 하나의 세션에서 여러 연결이 존재할 수 있음을 의미한다.

### 하나의 Master Secret 에서 새로운 Session Key 는 어떻게 만들지?

> 새로운 연결을 위해서 abbreviated handshake 를 하는데에 합의한 경우, Master Secret 을 재활용한다.이때 새로 생성할 Session Key 는 새 연결의 첫 메시지에서 서로가 보내는 Client Random, Server Random 을 사용하여 다시 도출하는 것이다. 따라서 Master Secret 은 유지하더라도 새로운 Session Key(대칭키) 를 만들 수 있는 것이다.

[How long does an HTTPS symmetric key last](https://security.stackexchange.com/questions/55454/how-long-does-an-https-symmetric-key-last)

[How does server use session key after tls handshake?](https://security.stackexchange.com/questions/194723/how-does-server-use-session-key-after-tls-handshake)

### 추가

- Session resumption: 다른 TCP 연결 위에 이전 TLS 세션을 사용하는 것.
	- 클라이언트와 서버가 이전에 유효한 TLS 세션이 있음을 확인하고 새로운 TCP 연결에 이전 TLS 세션을 사용 (재시작) 하기로 합의하는 것이다.
- Session renegotiation: 같은 TCP 연결 위에 다른 TLS 세션을 맺는 것.
	- 이전과 다른 파라미터 (양측의 Random 값, premaster secret 등으로 추정됨) 를 교환하여 새로운 handshake 를 하는 것.
<br><br>
# 참고 링크

[What happens in a TLS handshake? | SSL handshake | Cloudflare](https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake/)

[Differences between the terms "pre-master secret", "master secret", "private key", and "shared secret"?](https://crypto.stackexchange.com/questions/27131/differences-between-the-terms-pre-master-secret-master-secret-private-key)

[HTTPS - 2. HTTPS의 Ciphersuite, Handshake, Key derivation](https://dokydoky.tistory.com/463)

[HTTPS와 SSL 인증서 - 생활코딩](https://opentutorials.org/course/228/4894)

[전송 계층 보안 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%EC%A0%84%EC%86%A1_%EA%B3%84%EC%B8%B5_%EB%B3%B4%EC%95%88)
