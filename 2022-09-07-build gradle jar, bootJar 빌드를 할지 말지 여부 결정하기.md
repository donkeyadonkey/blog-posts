---
aliases: []
categories: []
date-created: September 7, 2022 6:42 PM
tags: [개발 일반]
date-updated: 2023-02-27 05:59
---
<br><br>
## 상황

plain jar 가 만들어지기 싫어서 

```java
jar.enabled(false)
```

를 쓰거나,

Spring application 실행 모듈이 아니라서 

```java
bootJar.enabled(false)
```

 를 쓰곤 한다.

### 모듈 A 에서 jar 빌드를 하지 않는 것은 A 에 의존하는 다른 모듈 B 가 빌드될 때 A 의 내용을 가져와 빌드 할 수 없다.

따라서 다른 모듈들이 의존하는 프로젝트, 모듈의 경우에는 꼭 jar 빌드를 해주어야 한다.