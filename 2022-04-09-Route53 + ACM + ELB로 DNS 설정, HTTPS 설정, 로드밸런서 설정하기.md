---
aliases: []
categories: []
date-created: April 9, 2022 10:36 PM
tags:
  - Web
date-updated: 2023-02-27 05:57
---
<br><br>
## 개념 정리

[2022-04-09-도메인과 DNS 서버](2022-04-09-도메인과%20DNS%20서버.md)

[2022-04-08-HTTPS와 SSL(TLS) HandShake](2022-04-08-HTTPS와%20SSL(TLS)%20HandShake.md)
<br><br>
## 요약

> 내 도메인을 애플리케이션 서버와 연결하는 과정이다.
>
> - 도메인 관리와 AWS 서비스들과 원활한 연결을 위해 Route53 을 사용한다.
> - HTTPS 설정을 위한 SSL 인증서 발행을 위해 ACM 을 사용한다.
> - 간편한 SSL 인증서 적용과 서버의 로드밸런싱 (추후 서버 추가 예정) 을 위해 ALB 를 사용한다.

> 아래는 전체적인 관계를 표현해본 그림이다.
>
> - 할때에는 너무 많은 서비스들이 섞여있어서 헷갈렸는데 결국은 `내 도메인을 어디에 연결하는가?` 만 생각하면서 하다보면 생각보다 복잡하지 않다.
> - 도메인 설정의 핵심은 도메인의 레코드들을 설정하는 것이다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/18c4b3dfedc4e58ffc07a6303954e87a.png)
<br><br>
# 과정

> 정확히 글 순서대로 이루어지는 것은 아니다. 순서를 미리 따져본다면,
>
> - 도메인 구매
> - → Route 53 호스팅 영역 생성
> - → SSL 인증서 발급 및 검증
> - → 로드밸런서 생성 & 인증서 등록
> - → Route 53 에서 도메인 A 레코드 로드밸런서 주소로 설정
> - → 세부적인 구성 확인
> 	- 로드밸런서 리스너
> 	- 보안그룹 적절하게 열려있는지 등
<br><br>
## 1. 도메인 구매

- gabia 에서 도메인을 2000 원 주고 구매하였다.
- gabia 는 도메인 판매 사업자이고 도메인을 구메하면 나는 이 도메인에 대한 소유권을 인증할 수 있다.

> 아래와 같이 가비아 사이트에서 도메인의 네임서버를 설정할 때 소유자를 인증한다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/d5b877908bef2be607a6ce6d13368f4b.png)
<br><br>
## 2. ACM: SSL/TLS 인증서 발급

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/c914f4a21d17949fbed797bd99d50d21.png)

- 퍼블릭 인증서를 만들자.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/0badf5de3130bc630d9fd99face4b2e0.png)

- 설정할 것이 별로 없다. 내 도메인 이름을 적어주고 DNS 검증으로 요청한다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/dc525ea0a0d348a0e9fd936dbec49637.png)

- 위와 같이 바로 요청되고 검증대기중이라고 뜬다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/fabc91bc53b7a97e3cef1516dcdf332b.png)

- 인증서는 해당 도메인에 CNAME 레코드를 등록할 수 있는지 확인하는 방식으로 소유자를 확인한다.
- 단순히 Route 53 에 등록되는 것이 아니라 실제 네임서버에 CNAME 레코드가 반영되어야 인증서가 발급된다.
	- 즉, 자신의 도메인의 네임서버를 설정할 수 있고, CNAME 레코드도 반영시킬 수 있다면 도메인을 소유한 사람이라고 검증하는 것이다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/22afc250d5ba1819c5d71e9091098643.png)

- 인증서 발급과정에서 Route 53 에서 레코드를 생성하도록 해주니 생성해준다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/465c738eee06611d512c7df87eb20ac6.png)

- Route 53 에서 확인해보면 CNAME 레코드가 생성되었고 네임서버에 반영하고 있을 것이다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/ed5f580978c23ac8916e8cbc5e2d4e93.png)

- 정상적으로 처리되면  위와 같이 발급됨 표시가 뜬다.

### ※ 인증서를 발급하는 리전에 관해서

> ACM 을 통한 SSL 인증서는 리전 별로 발급받을 수 있다.
>
> - CloudFront 를 사용하는 경우에는 꼭 미국 동부 (버지니아 북부) 에서 발급된 인증서만 사용할 수 있다고 한다.
> - 한편 아래에서 로드밸런서를 설정할 때 등록하는 인증서는 같은 리전에 있는 인증서만 인식할 수 있다.
> - 나 같은 경우에는 서울리전의 ec2, 로드밸런서를 만들고 있으니 서울 리전의 인증서를 발급한 것.
> - 만약 CloudFront 를 사용하여 배포한다거나 하면 버지니아 북부 인증서를 따로 발급받아야 한다.
> 	- 인증서는 도메인에 대해 발급하는 것이고 CNAME 도 똑같이 등록되므로 위와 같은 방법으로 여러 리전에 인증서를 쉽게 발급받을 수 있다.
> 	- Route 53 은 글로벌 서비스이므로 하나의 hosting zone 으로 여러 리전의 SSL Certificate 들이 상관없이 다 적용된다.
<br><br>
## 3. Route53 설정

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/839991701d7ef0b5591df2a98eae469f.png)

- 호스팅 영역에서 생성을 클릭하여 도메인을 등록한다.
- 나는 외부 업체에서 샀기 때문에 위와 같이 바로 등록하였다.
- Route 53 에서 도메인을 직접 구매하여 등록할 수도 있다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/d7770eb07456f3d8f5348512f7de81f8.png)

- 호스팅 영역을 만들면 위와 같이 등록이 된다. 이제부터 이 도메인을 Route 53 이 관리하도록 설정해주어야 한다.
- 등록하면 기본적으로 이 도메인에 대한 가장 필수적인 레코드인 NS 와 SOA 레코드가 할당해준다.
- 이때 NS 레코드는 이 도메인에 대한 네임서버 (Authoritative DNS 서버) 를 의미한다.

### 네임 서버 등록

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/e61503d156c3d5492f9b969b77eda4ec.png)

- 위와 같이 NS 레코드가 4 개 할당되었다.
- 이 도메인에 대한 네임서버를 aws 의 네임서버로 등록해주어야 브라우저가 인터넷 사업자 (또는 Recursive DNS 서버) 들을 통해 이 도메인의 목적지를 찾아갈 때 Route 53 이 관리하는 네임서버로 찾아가는 것이다.
- 할당받은 4 개의 DNS 서버 주소를 가비아에 가서 등록해준다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/d5b877908bef2be607a6ce6d13368f4b.png)

### A 타입 레코드 생성

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/241807be8987d3086b2a315aaee7e742.png)

- 특별히 쓰고 싶은 정책이 있다면 쓰고 아니면 단순 라우팅으로 진행한다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/816ce3ad78792cfdb612c5f55baf3240.png)

- 기본 도메인을 쓸 수도 있고 별칭 도메인을 쓸 수도 있다.
- A 레코드에 대한 값으로 로드밸런서를 설정해줄 것이다.
- 그러면 로드밸런서에서 인증서 설정 등을 사용할 수 있는 것이다.
- 생성하면 Route 53 호스팅 영역의 해당 도메인에 A 레코드가 생긴다.
<br><br>
## 4. 로드밸런서 설정

### 목적

- 가장 큰 목적은 HTTPS 트래픽을 처리하는 것이다. 이를 로드밸런서를 통해 이룰 수 있다.
- 트래픽 분산을 위한 기능도 물론 한다.

### 만약 ALB 를 안쓴다면

- 다른 방법으로 nginx 등의 서버를 목표 애플리케이션 앞에 둬서 로드밸런싱 하기도 한다.
	- 배포 중에는 다른 포트를 가진 서버로 연결해준다든지, 개발/운영 서버를 나눈다든지
- 다른 로드밸런서 서버를 둔다면 HTTPS 연결을 위해서 그 서버 안에 SSL 인증서를 위치시켜 등록해두면 HTTPS 연결이 가능하다.
- 나는 ALB 를 쓰므로 간단하게 ACM 을 연동해줘서 별도로 서버에 인증서를 발행하는 등의 과정 없이 콘솔 상의 설정으로 모두 처리할 수 있었다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/479d3da1607c48019489b9049d9edb81.png)

- Application Load Balancer 를 선택

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/5b2917a19f6b49c90983a189c868cbca.png)

- 적당히 이름 설정하고 Internet-facing,  IPv4 를 설정해주었다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/d3d528405592aa34ec436625778530b9.png)

- 나는 4 개 AZ(가용영역, Availability Zone) 모두 선택해 주었다.
- 인터넷 게이트웨이가 설정된 VPC 만 설정 가능하다고 한다. 나는 디폴트로 다 되어있었다.
- 로드밸런서가 생성된 Zone 은 바로 삭제될 수 없다고 한다.
- 보통 그냥 기본 설정된 VPC 와 서브넷들을 사용하면 된다. 로드밸런서가 처리할 AZ 을 나누어 관리할 이유가 있다면 적당히 설정하면 될 듯하다. (알아서 할 사람이면 이 글을 볼 일이 없을 듯.)

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/449e3ce28aaf40e3a38e52baf38fa549.png)

- 보안 그룹은 내가 연결하고자 하는 EC2 인스턴스와 같은 것으로 선택해 주었다.
- 인바운드 규칙 편집에서 어떤 프로토콜의 어떤 포트의 어떤 IP 범위가 들어오는지 설정해주는 것을 생각해보면 같은 것으로 설정해주면 될 듯 하다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/f7efa36582d5a6bb44ef49bd8508e31b.png)

- 가장 핵심적인 부분이다. 어떤 포트를 들을지 listener 를 정하고 그 포트에 대한 target group 을 지정해준다.
	- 타겟 그룹이 없는 경우 만들어준다. 아래에서 설명한다.
	- 나는 HTTPS 의 443 포트와 HTTP 의 80 포트의 트래픽을 받아서 특정 타겟그룹으로 가도록 매칭시켜주었다.
	- HTTP 를 HTTPS 로 만들기 위해 redirect 하는 기능 (을 비롯하여 다양한 listener rules) 은 로드밸런서를 만들고 나서 편집할 수 있다. 따라서 기본적인 룰만 설정해준다.
- 또한, HTTPS 를 위해 SSL 인증서를 설정해주어야 한다.
	- 나는 ACM 에서 만들어둔 인증서를 등록하였다.
	- 같은 리전의 인증서만 검색이 된다.
- Security policy 는 기본 선택값으로 진행하였다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/54c75de233dde488cfe87c29e8ac243c.png)

- 타겟 그룹에서는 간단하게 말해서 트래픽이 몇번 포트로 나갈것인지를 말한다.
- 나는 EC2 인스턴스의 HTTP 8080 포트로 갈 것이므로 그와 같이 설정해 주었다.
- 로드밸런서가 체크하는 Health check 등 기능도 있다. 모두 기본값으로 설정해 주었다.

로드밸런서 설정은 위와같이 하고 만든다. 그리고나서 리스너 설정을 수정한다.

### 리스너 규칙 수정

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/2a2c74b5735aa46f0b9068b7a453065c.png)

- 443 포트는 그대로 8080 포트의 타겟그룹으로 설정해주면 된다.
- 그 외의 포트들은 443 포트로 리다이렉트 되도록 설정해준다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/e74c9ed261abc332f9daedb3a6842102.png)

- 연필 아이콘을 찾아서 수정해주면 된다.
- 리다이렉트 status code 로 301, 302 가 있는데 나는 301 로 하였다.
- 웹사이트들 curl 해보니 HTTPS 요청을 제외하고는 보통 301 이 뜬다.
	- curl <https://www.naver.com> 은 컨텐츠를 보여줌
	- curl [naver.com](http://naver.com) 등은 모두 301 로 뜸. 다 리다이렉트 하는 것이 아닐까.
<br><br>
## 5. 보안 그룹 확인

> EC2 인스턴스와 로드밸런서에서 설정해준 보안그룹의 인바운드가 열려있는지 확인해주자.
>
> - 나는 HTTPS 프로토콜의 443 포트로 트래픽을 받으려 하므로 이를 열어준다.
<br><br>
# 다음으로

- 공부도 되고 좋았다. 네트워크 관련 지식이 많이 부족함을 알게 되어 짬짬이 정리할 필요성을 느꼈다.
- HTTPS 되는 것을 보고 기뻤는데, AWS 에 의존하지 않고 직접 발급받아서 운영해 보고 싶다.
- 인프라 설계와 관련하여 아쉬움이 있다. 트래픽 분산과 관련한 설계는 아직 제대로 하지 않았기 때문에 추가적인 서버를 구성하여 무중단 배포를 할 수 있도록 만들어 보고 싶다.
<br><br>
# 참고

[[매뉴얼][초보자를 위한 AWS 웹구축] 8. 무료 도메인으로 Route 53 등록 및 ELB 연결](https://tech.cloud.nongshim.co.kr/2018/10/18/%EC%B4%88%EB%B3%B4%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-aws-%EC%9B%B9%EA%B5%AC%EC%B6%95-8-%EB%AC%B4%EB%A3%8C-%EB%8F%84%EB%A9%94%EC%9D%B8%EC%9C%BC%EB%A1%9C-route-53-%EB%93%B1%EB%A1%9D-%EB%B0%8F-elb/)

[52. Route53 이란?](https://brunch.co.kr/@topasvga/49)

[새 도메인 이름 구입한 후 AWS에서 해야할 일](https://musma.github.io/2019/09/16/what-to-do-after-you-buy-your-new-domain-on-aws.html)
