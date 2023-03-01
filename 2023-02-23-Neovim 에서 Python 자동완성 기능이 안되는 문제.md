---
aliases: []
date-created: 2023-02-23 10:54
date-updated: 2023-02-27 06:09
tags:
  - python
  - lsp
categories:
  - 조각글
---

<br><br>
## 상황

먼저, LSP 설정, 파이썬 language server(pyright) 도 모두 설치된 상태.

하지만 자동완성이 되지 않았다. 가상환경 세팅 떄문인것으로 판단하고 시도해 보았다.

가상환경을 인식하지 못해서 에러를 알려준다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/4afcf2a04e47568a566c00d4e6ad2e3a.png)
<br><br>
## 해결

프로젝트 디렉토리에서 세팅해둔 가상환경을 구동한 뒤 다시 시도하면,

```
pipenv shell
```

아래와 같이 정상적으로 작동하고, 특정 라이브러리가 제공하는 코드도 자동완성이 가능하다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/29e5e43cbfed6a2e8beffbde835ac982.png)
