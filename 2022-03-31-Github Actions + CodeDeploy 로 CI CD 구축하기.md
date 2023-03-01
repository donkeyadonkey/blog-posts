---
aliases: []
date-created: March 31, 2022 3:25 PM
Date: March 31, 2022 → April 2, 2022
tags:
  - Infra
date-updated: 2023-02-27 05:55
categories:
  - 조각글
---
<br><br>
# 개요: Github Actions 개념

[2022-03-31-Github Actions](2022-03-31-Github%20Actions.md)

[2022-04-10-CodeDeploy에 관하여](2022-04-10-CodeDeploy에%20관하여.md)
<br><br>
# .github/workflows/***.yml

> 깃헙액션이 동작하는 것은 위 경로의 yml 파일로 정의한다.
<br><br>
## 1. gradle 빌드 명령

```yaml
- name: Build with Gradle
      uses: gradle/gradle-build-action@v2
      with:
        arguments: build
```

> `gradle/gradle-build-action`

- 아래 리포지토리에서 개발된다.

<https://github.com/gradle/gradle-build-action>

> `./gradlew build` 를 사용하는 방법도 있지만 기본적으로 깃헙에서 만들어주는 파일은 `gradle/gradle-build-action` 를 사용한다. 알아보니 깃헙 액션에서 사용하기 위한 명령의 한 종류인 듯.
>
> - 그리고, 기본으로 만들면 아래와 같이 특정 커밋의 해쉬값이 붙어있는데, 그냥 버전 이름 (v2 등) 으로 대체해도 된다. gradle 리포지토리에서 찾아보니 특정 날짜의 커밋 해쉬값이었다.
> - 왜 쓰는 지는 해당 리포지토리에서 설명하고 있다. gradle 버전을 지정할 수 있고 빌드 성능을 향상시킬 수 있는 기능들이 있는 듯 하다. cache 관련 설명이 있는데 정확히 파악은 어렵다.
>
> ![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/5360773e7c3ad6898f5d56102a0a3d81.png)

^ged83v

Github actions 의 설명 문서 ^coyha8

[Building and testing Java with Gradle - GitHub Docs](https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-java-with-gradle)
<br><br>
## 2. AWS credential 설정

```yaml
- name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ${{ secrets.AWS_REGION }}
```

> gradle 의 경우와 마찬가지로 github actions 에서 사용하는 명령어 를 사용한다.
>
> - 나는 적절한 aws 관련 정보를 입력해주면 된다.
> - 민감 정보이므로 github 환경변수로 넣어주자.
> - 명령어 리포지토리
>
> <https://github.com/aws-actions/configure-aws-credentials>
<br><br>
## 민감 정보 환경변수화 하기

[GitHub Actions - 환경변수 등록 방법](https://dnight.tistory.com/entry/GitHub-Actions-%ED%99%98%EA%B2%BD%EB%B3%80%EC%88%98-%EB%93%B1%EB%A1%9D-%EB%B0%A9%EB%B2%95)

- db url
- db username ^p5khca
- db password
- mq username
- mq password
- access token
- secret token
- 등

### 특징

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/ad4e4e4f3b417a04e409888b9acaf4d9.png)

- 등록된 환경변수들은 repository 단위로 관리된다.
- `${{ secrets.변수명 }}` 으로 사용할 수 있다.

### 대략적인 과정

github 에 환경변수 등록 → application.yml 등에서 환경변수를 적용 → 애플리케이션 코드에서 이 설정을 사용
<br><br>
## 3. 코드를 S3 에 올리기 위해 zip 파일로 압축하기

```yaml
- name: Make zip file
  run: zip -r ./$GITHUB_SHA.zip .
  shell: bash
```
<br><br>
## 4. S3 업로드 명령어 넣기

```yaml
- name: Upload to S3
      run: aws s3 cp --region ap-northeast-2 ./$GITHUB_SHA.zip s3://$S3_BUCKET_NAME/$GITHUB_SHA.zip
```

> `$GITHUB_SHA` 는 github actions 에서 사용하는 환경변수로, 현재 워크플로우가 실행되는 커밋의 해쉬값.

> `$S3_BUCKET_NAME` 는 지금 설정 파일의 상단 env 에 정의 해둔 S3 버킷 이름을 가져온 것이다. 자기 버킷 이름을 넣으면 된다.
>
> - 해당 버킷에 zip 파일을 저장할 때 중복을 피하기 위해 `$GITHUB_SHA.zip` 으로 저장한다.

### ※ 궁금증: 버킷 이름만 줬는데 어떻게 찾아가지?

[Buckets overview](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingBucket.html)

→ 버킷 이름은 partition 내에서 유일하다고 한다. 한 region 도 초월해서 partition 내면 유일함.

→ partition 은 AWS 내에 3 개 라고 함.

→ 그래서 Region 과 버킷 이름을 주면 유일하게 찾아갈 수 있는 것.

> 앱 이름으로 대강 만들었는데 뚝딱 만들어져서 유일한 건지 감이 안왔었다...

<aside>
📌 “Amazon S3 supports global buckets, which means that each bucket name must be unique across all AWS accounts in all the AWS Regions within a partition. A partition is a grouping of Regions. AWS currently has three partitions: 
`aws`(Standard Regions), 
`aws-cn`(China Regions), and 
`aws-us-gov`(AWS GovCloud (US)).”

</aside>
<br><br>
## 5. Code Deploy 실행 명령

```yaml
- name: Code Deploy
    run: aws deploy create-deployment 
			--application-name cuppa-backend
      --deployment-config-name CodeDeployDefault.OneAtATime
      --deployment-group-name cuppa-backend-deploy
      --s3-location bucket=$S3_BUCKET_NAME,bundleType=zip,key=$GITHUB_SHA.zip
```

> “이 버킷에 찾아가서 이 애플리케이션을 이런이런 옵션으로 배포하세요”

- `aws deploy create-deployment`
- `--application-name` : 애플리케이션 이름을 지정한다.
- `--deployment-config-name` : 배포 방법을 설정한다. 미리 정의된 몇가지 방법이 있다. 아래 링크에서 찾아서 사용할 수 있다.
- `--deployment-group-name` : 배포 그룹 이름을 명시해준다
- `--s3-location` : 버킷 위치와 올린 압축파일의 정보를 준다.

[CodeDeploy에서 배포 구성 작업](https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/deployment-configurations.html)

<aside>
📌 이제 배포할 코드를 EC2 로 넘겨줄 것이다. EC2 에서 받은 코드를 실행할 명령을 만들어야 한다.
CodeDeploy 는 EC2 에서 appspec.yml 과 .sh 파일을 바탕으로 실행할 것이다.

</aside>

### 오류

yml 파일에 오류가 있을 경우 다음과 같은 화면이 나올 수 있다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/697e090c57a7eece16b5b94a7aac1dd1.png)

> 여기부터 어떤 방식으로 할지 고민을 했다.

1. Github actions 에서 test, gradle build

→ docker image build

→ docker hub 에 push

→ 바로 EC2 에 명령어를 줘서 docker hub 에서 이미지 pull

→ docker run

> 이 모든걸 github actions 의 yml 파일로 설정이 가능하다.
> 다른 S3 나 CodeDeploy 가 개입할 필요가 없으므로 비교적 간단한 방법이다.

1. Github actions 에서 test, gradle build

→ S3 로 jar 파일을 압축해서 전달

→ CodeDeploy 가 소스코드를 EC2 로 전달하여 [deploy.sh](http://deploy.sh) 스크립트 실행

: [deploy.sh](http://deploy.sh) 는 docker image 를 build, run 한다.

> 이렇게 하면 docker hub 를 사용하지 않고 aws 에만 의존하게 된다.
<br><br>
# CodeDeploy 가 사용할 빌드 명령

### appspec.yml

> S3 로 부터 넘겨 받은 압축 파일을 EC2 인스턴스 상에서 어떤 위치에 둘지 등을 지정한다.
>
> - CodeDeploy 가 EC2 에 접근권한도 있어야 하므로 지정해준다.
> - 자세한 설명은 공식 문서와 다음 페이지를 참조
>
> [2022-04-10-CodeDeploy에 관하여](2022-04-10-CodeDeploy에%20관하여.md)

```bash
version: 0.0
os: linux
files:
  - source:  /
    destination: /home/ubuntu/cuppa
    overwrite: yes

permissions:
  - object: /
    pattern: "**"
    owner: ubuntu
    group: ubuntu

hooks:
  BeforeInstall:
    - location: scripts/before_deploy.sh
      runas: ubuntu

  AfterInstall:
    - location: scripts/deploy.sh
      timeout: 120
      runas: ubuntu
```

### deploy.sh

> 배포할 때 실행할 명령어를 스크립트로 지정한다.
>
> - 상황에 따라 너무 많이 달라지는 부분이다.
> - 꼭 배포 실행 말고도 위의 여러 Event, hook 에 따라 스크립트를 만들어 지정해주면 된다.
> - 나는 Docker 컨테이너를 실행하는 명령을 넣었다.

[github action으로 ec2에 자동배포하기3](https://ms3864.tistory.com/383?category=1003779)

[[Docker] git action으로 CI/CD 구축하기](https://soohyun6879.tistory.com/248)

[Github actions를 이용한 CICD - 1](https://itcoin.tistory.com/684?category=769089)

[Github actions를 이용한 CICD - 2](https://itcoin.tistory.com/685)

[[AWS] Spring + Github Actions + CodeDeploy로 CI/CD 하는 법](https://devlog-wjdrbs96.tistory.com/361)

[[AWS] Spring + Github Actions + CodeDeploy로 CI/CD 하기 2편](https://devlog-wjdrbs96.tistory.com/362?category=885022)
