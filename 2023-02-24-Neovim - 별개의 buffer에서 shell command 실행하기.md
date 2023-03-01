---
aliases: []
date-created: 2023-02-24 12:24
date-updated: 2023-02-27 06:11
tags: [neovim]
categories: [조각글]
---
<br><br>
## Buffer 안에서 터미널 명령어 실행하기

```shell
te {커맨드}
```
<br><br>
## 결과

아래와 같이 `<C-w>v` 를 통해 윈도우를 나눈 뒤,

```shell
te bundle exec jekyll serve 
```

커맨드를 실행한 모습이다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/49d34a50890dfc03fc13a4b4941afa9a.png)
<br><br>
## 참고

[vim - How to run scripts in background in Neovim? - Stack Overflow](https://stackoverflow.com/questions/46097571/how-to-run-scripts-in-background-in-neovim)
