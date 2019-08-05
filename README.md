# jslee's blog

## 개요

Hexo를 이용하여 구축한 블로그이다.

NexT 테마를 사용한다.

## 사용법

이 레포지토리를 클론한 후 다음 명령을 실행한다.

``` bash
npm install

sed -i "s/___VERSION___/TEST/" themes/next/_config.in.yml > themes/next/_config.yml

hexo clean
hexo generate
hexo algolia
hexo deploy
```

연결된 GitHub Pages 서비스에서 배포된 블로그를 확인할 수 있다.
