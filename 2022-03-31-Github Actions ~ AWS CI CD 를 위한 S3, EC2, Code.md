---
aliases: []
categories: [조각글, ]
date-created: March 31, 2022 3:28 PM
tags:
  - Infra
date-updated: 2023-02-27 05:55
---

[2022-03-31-Github Actions](2022-03-31-Github%20Actions.md)
<br><br>
## S3 버킷 만들기

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/a821dfb848236c43b3e79ce09a58b7b1.png)
<br><br>
## EC2 인스턴스 접속

[2022-03-31-EC2 인스턴스에 접속하기](2022-03-31-EC2%20인스턴스에%20접속하기.md)
<br><br>
## CodeDeploy 에이전트 설치

[Ubuntu Server용 CodeDeploy 에이전트 설치](https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/codedeploy-agent-operations-install-ubuntu.html)

> CodeDeploy 가 있는 버킷에서 리소스를 받아온다.
>
> 이 버킷에 대한 정보는 아래 링크에서 찾을 수 있다.
>
> [CodeDeploy 리소스 키트 참조](https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/resource-kit.html#resource-kit-bucket-names)

```bash
wget https://aws-codedeploy-ap-northeast-2.s3.ap-northeast-2.amazonaws.com/latest/install
```

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/38fd5d328ceee3a893a9b6cfa06201e4.png)

### 실행 확인하기

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/33d83725c4bad3f7bed26bbd9fbb70be.png)

### ※ 실행 에러면

> 시작 명령을 주거나, 상태를 확인하고 재시작하도록 한다.

```bash
sudo service codedeploy-agent start
```

```bash
sudo service codedeploy-agent status
```
<br><br>
## EC2 IAM 설정

> EC2 가 S3, CodeDeploy 를 이용할 수 있도록 권한을 설정해준다.

### IAM 역할 만들기

### EC2 인스턴스에 역할 부여하기

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/5534464ba26a78731f569a2625c3a6e6.png)

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/5634e8174903066bce947a6d891a1681.png)

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/7e9ff5a679847b321826a3e305451cdf.png)

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/5c1a3fba3945d6a8789cfe79fcef7248.png)

### CodeDeploy 애플리케이션 생성

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/7e5bf1eb800ad6dfc2811f1bd25a4ea6.png)

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/54504edb9139f6c93dfe48b779c6608a.png)

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/031aba631e8ae74e3be1a39ebfc1701a.png)

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/82ca63cac3fc3575b5758f6e05459291.png)

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/384d81ee676fc78f5adfea94f5342324.png)
<br><br>
## GIthub Actions 를 위해 AWS  IAM 사용자 추가

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/2002b1004154e907236436133852ea91.png)

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/95c2f38122a889b5a229f366b1c765c7.png)

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/3c1e41120851cf38f902626cabc97d45.png)

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/3c0a1ef51c2256201a09fc7a8abb641b.png)
<br><br>
# 에러

[InstanceAgent::Plugins::CodeDeployPlugin::CommandPoller: Missing credentials](https://www.notion.so/InstanceAgent-Plugins-CodeDeployPlugin-CommandPoller-Missing-credentials-cf40ef7b63594a719198f29f45cd09d4)

[EC2가 뻗는 문제](https://www.notion.so/EC2-88562a52c08c4cad8018636a0d8b1924)
<br><br>
# 참고

[github action으로 ec2에 자동배포하기2](https://ms3864.tistory.com/382?category=1003779)
