---
aliases: []
categories: []
date-created: June 11, 2022 9:00 PM
tags: [Database]
date-updated: 2023-02-27 05:58
---

<aside>
📌 2038 년도 이상에서 오류가 나면 이해가 되는데, 특정 범위까지는 함수가 잘 작동하는 것이 이상했다.

</aside>

### 내 mysql 환경

mysql Ver 8.0.28 for macos11.6 on x86_64 (Homebrew)

### 해당 함수 문서

[MySQL :: MySQL 8.0 Reference Manual :: 12.7 Date and Time Functions](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_unix-timestamp)

- mysql 8.0.28 에서 추가된 내용이다.

<aside>
📌 Prior to MySQL 8.0.28, the valid range of argument values is the same as for the `[TIMESTAMP](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)`
 data type: `'1970-01-01 00:00:01.000000'`
 UTC to `'2038-01-19 03:14:07.999999'`
 UTC. This is also the case in MySQL 8.0.28 and later for 32-bit platforms. For MySQL 8.0.28 and later running on 64-bit platforms, the valid range of argument values for `UNIX_TIMESTAMP()`
 is `'1970-01-01 00:00:01.000000'`
 UTC to `'3001-01-19 03:14:07.999999'`
 UTC (corresponding to 32536771199.999999 seconds).

</aside>
<br><br>
## 확인

> 문서에 있었는데 노가다 했었음.
> 
- 3001-01-19 까지는 정상적으로 반환하는데
- 3001-01-20 은 0 을 반환한다.

### 특정 컬럼의 타입을 bigint 로 주더라도 모두 활용하지 못함.

- 차라리 int 로 해서 2038 년 까지 쓰는게 효율적이다.
- 아니면 timestamp 타입으로 사용하던가.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/196cd2d2b49bb6c4e204f6642a3e6df4.png)
<br><br>
## 추가적인 확인

> 전체 타임스템프에선 아래와 같이 나온다.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/88f9d14129a161c35999e71ac232474f.png)
