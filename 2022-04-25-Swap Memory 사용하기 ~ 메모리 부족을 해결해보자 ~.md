---
aliases: []
categories: []
date-created: April 25, 2022 2:22 PM
tags: [OS]
진행상태: 2. In Progress 실제 진행중
date-updated: 2023-02-27 05:58
---
<br><br>
# 무엇?

### 스왑 메모리

> 실제 메모리가 가득 찼을 때 디스크 (HDD 등) 공간으로 대체하는 것.
> 
> - 속도는 매우 느려질 것.
> - 하지만 메모리 부족으로 뻗는 것 보다는 안정적이게 됨.

<aside>
📌 ec2 가 자꾸 뻗어서 로그를 보니 메모리가 부족하단다. 그래서 방법을 찾아봤다.

</aside>

### 두가지 방식

- 스왑 파티션
- 스왑 파일

> 스왑 파일 방식이 성능은 떨어질 수 있지만 간단해보인다.
> 
> 
> 파일 시스템이 개입하므로 디스크 내에 연속된 공간을 할당받지 못할 수도 있다.
> 
<br><br>
# 과정
<br><br>
## 1. 스왑 파일 생성하기

```bash
sudo dd if=/dev/zero of=/swapfile bs=128M count=16
```

### dd

> 블록 단위로 파일을 복사하거나 변환하는 명령어
> 
> - if: 인풋 파일
> - of: 아웃풋 파일
> - bs: 한번 읽을 때 최대 바이트 크기
> - count: 블록 수

### /dev/zero

> 널문자를 무한히 반환하는 파일
> 
> - 메모리에 매핑될 때 익명 메모리를 사용하는 것과 동일한 효과

[/dev/zero - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki//dev/zero)

[/dev/zero - 제타위키](https://zetawiki.com/wiki//dev/zero)

- 즉, 플이하면 널문자를 /swapfile 에 128M 의 볼륨으로 16 개의 블록을 만들겠다.
- 따라서 /swapfile 은 약 2GB 만큼의 용량을 차지하는데, 널 값으로 차있으므로 익명 메모리 2GB 와 대응된다.
- 아래에서 이 파일이 차지하는 공간을 스왑 메모리로 사용하기 위해 만드는 것임.
<br><br>
## 2. 스왑 파일 권한 변경

```bash
sudo chmod 600 /swapfile
```
<br><br>
## 3. 스왑 영역 설정

```bash
sudo mkswap /swapfile
```

> 이 파일로 메모리 스왑 영역을 만듬
> 
<br><br>
## 4. 사용할 수 있도록 추가

```bash
sudo swapon /swapfile
```

> 즉시 사용함
> 
<br><br>
## 5. 확인 및 시스템에 등록

```bash
sudo swapon -s
```

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/535832d9650b8faabd654ab86da4a0e9.png)

```bash
sudo vi /etc/fstab
```

```bash
# 시스템 실행시 자동으로 해당 파일에 메모리 스왑 할당함
/swapfile swap swap defaults 0 0
```

1. 스왑파일 이름  : /swapfile
2. 마운트포인트 (스왑은 swap 이라하면됨) : swap
3. 파일시스템 종류 (스왑은 swap) : swap
4. 마운트 옵션: default
5. dump 백업 사용 유무 : 0
6. 파일시스템 체크 : 0
<br><br>
# 참고

[[AWS] 아마존 ec2 무료 티어 메모리 부족 현상](https://koonsland.tistory.com/79)

[[Linux] dd 명령어 옵션 정리](https://bigsun84.tistory.com/312)

[[리눅스] 스왑 메모리(Swap Memory) 메모리](https://scbyun.com/984)