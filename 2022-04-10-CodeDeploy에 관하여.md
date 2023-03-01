---
aliases: []
categories: []
date-created: April 10, 2022 7:49 PM
tags:
  - Infra
date-updated: 2023-02-27 05:56
---
<br><br>
# 문서

[CodeDeploy AppSpec 파일 참조](https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/reference-appspec-file.html#appspec-reference-server)

번역이 조악해서 원문이 낫다.
<br><br>
# AppSpec 파일

> CodeDeploy 가 수행할 일의 내용을 담은 파일로, yml 또는 json 형식의 파일이다.
>
> - EC2/온프레미스 컴퓨팅 플랫폼의 경우, yml 파일을 사용한다.
> - 소스코드 디렉토리의 루트경로에 위치해야한다.

> CodeDeploy 는 이 파일을 사용하여
>
> - S3 또는 Github 에서 인스턴스에 설치할 항목을 찾는다.
> - 배포 이벤트가 트리거 되었을 때 실행할 hooks 를 찾는다.

> 할일
>
> - AppSpec 을 포함한 애플리케이션 파일들을 zip 등으로 압축하여 S3 나 git repository 에 올려야한다.
<br><br>
## AppSpec 을 읽기 전에 수행되는 CodeDeploy CLI 명령

```bash
- name: Code Deploy
        run: aws deploy create-deployment
          --application-name cuppa-backend
          --deployment-config-name CodeDeployDefault.OneAtATime
          --deployment-group-name cuppa-backend-deploy
          --s3-location bucket=$S3_BUCKET_NAME,bundleType=zip,key=$GITHUB_SHA.zip
```

> github actions 배포파일에 정의한 명령 중 일부이다.
>
> 이 명령어로 CodeDeploy 가 실행된다.
>
> - 애플리케이션 이름
> - 배포 설정 (어떻게 배포할 것인지)
> - 배포 그룹 이름
> - 소스코드 위치 (S3 위치)
>
> 를 명시해준다.
<br><br>
## 파일 구조 설명

### 1. files

[AppSpec 'files' section (EC2/On-Premises deployments only)](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-files.html)

```bash
files:
  - source: source-file-location-1
    destination: destination-file-location-1
file_exists_behavior: DISALLOW|OVERWRITE|RETAIN
```

> 배포 과정의 “Install” event 에서 파일을 인스턴스로 복사하는 데에 필요한 정보이다.
>
> - source 를 `/` 로 설정하면 배포하고자 하는 소스코드 루트 디렉토리의 모든 파일을 말한다.
> 	- 즉, appspec.yml 이 위치하는 경로이다.
> - destination 은 인스턴스에 복사할 위치이다.
> - file_exists_behavior 는 파일이 이미 존재할 때 액션을 설정할 수 있다.

### 2. permissions

[AppSpec 'permissions' section (EC2/On-Premises deployments only)](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-permissions.html)

```bash
permissions:
  - object: object-specification
    pattern: pattern-specification
    except: exception-specification
    owner: owner-account-name
    group: group-name
    mode: mode-specification
    acls: 
      - acls-specification 
    context:
      user: user-specification
      type: type-specification
      range: range-specification
    type:
      - object-type
```

> 복사한 배포 파일에 권한을 설정한다.
>
> 만약 배포 파일이 루트권한이 필요하다면 CodeDeploy 가  이 파일에 대한 명령을 수행하지 못할 수도 있으므로.

### 3. hooks

[AppSpec 'hooks' section](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html)

> 순서대로 실행되는 배포 Event 들이다.
>
> 스크립트 파일로 각 event 에서 실행될 스크립트를 지정할 수 있다.

<aside>
📌 중요:  Start, DownloadBundle, Install, End 는 스크립트로 명령할 수 없다.

그 외는 원하는 대로 스크립팅 하자

</aside>

1. ApplicationStop
2. DownloadBundle: zip 파일을 인스턴스로 가져온다.
3. BeforInstall: 배포하기전에 할 행동을 설정할 수 있음. 백업한다든지, 현재 애플리케이션을 종료한다든지.(EC2 가 자꾸 죽어서 종료부터 했다. 이후 수정해야함.)
4. Install: zip 파일을 풀어서 지정 경로로 복사한다.
5. AfterInstall: 복사 후 할 액션.
6. ApplicationStart: 애플리케이션 시작.
7. ValidateService
<br><br>
# 참조

[[AWS] CodeDeploy appspec.yml 파헤치기](https://yoo11052.tistory.com/113)
