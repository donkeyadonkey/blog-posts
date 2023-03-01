---
aliases: []
categories: [조각글]
date-created: 2022-10-30 5:58 PM
Project: Recody
tags:
  - 개발 일반
date-updated: 2023-02-27 06:05
---

```java
dependencies {
    implementation project(":common")
    implementation project(":service-movie-api:core")
    implementation project(":service-music-api:core")
...
}
```

위와 같이 ‘core’ 라는 서브 모듈이 movie, music 에 각각 있다.

이때 각 프로젝트를 implementation 하면 한쪽이 정상적으로 인식되지 않는다.

[Projects with same name lead to unintended conflict resolution · Issue #847 · gradle/gradle](https://github.com/gradle/gradle/issues/847#issuecomment-262078306)

이때 ‘core’ 모듈이 빌드될 때 이름을 보면

com.recody.recodybackend:core 와 같은 형태로,

두가지 ‘core’ 모듈이 같아진다. 따라서 gradle 은 이 둘을 구별할 수 없다.

### 스프링의 모듈 네이밍

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/f8669c363d7d90e99554eaadfa106a13.png)

### 스프링 시큐리티 모듈 네이밍

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/b13b5ee8cc20ef98fe87cd2e23fe94cd.png)
<br><br>
## 따라서

모듈이름은 flat 하게 구성하는 것이 gradle 을 단순하게 사용하기에 좋을 것 같다.

다른 방법이 있을 수도 있겠지만 일단은 단순하고 예상치 못한 버그를 피하도록 해야겠다.
