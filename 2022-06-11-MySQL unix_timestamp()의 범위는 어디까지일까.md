---
aliases: []
categories: []
date-created: June 11, 2022 9:00 PM
tags: [Database]
date-updated: 2023-02-27 05:58
---

<aside>
π 2038 λλ μ΄μμμ μ€λ₯κ° λλ©΄ μ΄ν΄κ° λλλ°, νΉμ  λ²μκΉμ§λ ν¨μκ° μ μλνλ κ²μ΄ μ΄μνλ€.

</aside>

### λ΄ mysql νκ²½

mysql Ver 8.0.28 for macos11.6 on x86_64 (Homebrew)

### ν΄λΉ ν¨μ λ¬Έμ

[MySQL :: MySQL 8.0 Reference Manual :: 12.7 Date and Time Functions](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_unix-timestamp)

- mysql 8.0.28 μμ μΆκ°λ λ΄μ©μ΄λ€.

<aside>
π Prior to MySQL 8.0.28, the valid range of argument values is the same as for theΒ `[TIMESTAMP](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)`
Β data type:Β `'1970-01-01 00:00:01.000000'`
Β UTC toΒ `'2038-01-19 03:14:07.999999'`
Β UTC. This is also the case in MySQL 8.0.28 and later for 32-bit platforms. For MySQL 8.0.28 and later running on 64-bit platforms, the valid range of argument values forΒ `UNIX_TIMESTAMP()`
Β isΒ `'1970-01-01 00:00:01.000000'`
Β UTC toΒ `'3001-01-19 03:14:07.999999'`
Β UTC (corresponding to 32536771199.999999 seconds).

</aside>
<br><br>
## νμΈ

> λ¬Έμμ μμλλ° λΈκ°λ€ νμμ.
> 
- 3001-01-19 κΉμ§λ μ μμ μΌλ‘ λ°ννλλ°
- 3001-01-20 μ 0 μ λ°ννλ€.

### νΉμ  μ»¬λΌμ νμμ bigint λ‘ μ£ΌλλΌλ λͺ¨λ νμ©νμ§ λͺ»ν¨.

- μ°¨λΌλ¦¬ int λ‘ ν΄μ 2038 λ κΉμ§ μ°λκ² ν¨μ¨μ μ΄λ€.
- μλλ©΄ timestamp νμμΌλ‘ μ¬μ©νλκ°.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/196cd2d2b49bb6c4e204f6642a3e6df4.png)
<br><br>
## μΆκ°μ μΈ νμΈ

> μ μ²΄ νμμ€ννμμ  μλμ κ°μ΄ λμ¨λ€.

![image](https://s3.ap-northeast-2.amazonaws.com/donkeyadonkey-assets/img/88f9d14129a161c35999e71ac232474f.png)
