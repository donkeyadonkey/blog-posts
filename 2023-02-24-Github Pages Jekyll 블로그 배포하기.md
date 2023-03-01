---
aliases: []
date-created: 2023-02-24 09:57
date-updated: 2023-02-27 06:10
tags:
  - jekyll
  - github pages
  - troubleshooting
categories:
  - 조각글
  - troubleshooting
---
<br><br>
## 문제

새로운 테마를 포크해서 master 에 약간의 수정을 한 뒤 배포해보았는데, 정상적으로 뜨지 않는 문제가 있었다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/25e0690caa0c0012191e372ea90908e2.png)

위와 같이 `index.html` 페이지가 렌더링되지 않고 그래로 출력되었다. 루비 환경 세팅이나 jekyll 엔진이 작동하지 않는 것으로 보였다.

약간 서치해보니 pages 에 배포하는 방법에 변경점이 있는 것 같았다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/7fbc46ecc84dc8db0f268e1dfcb34e27.png)
<br><br>
## 해결

1. Settings -> Pages -> Build and deployment -> Source 항목을 GIthub Actions 로 바꿔준다.
2. 추천해주는 actions 설정파일 (`jekyll.yml`) 을 생성하는 커밋을 한다.
		1. 별다른 수정을 하지 않아도 된다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/8fffc575e1573cea16fe143233e21341.png)
