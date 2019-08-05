# jslee's blog

## 개요

Hexo를 이용하여 구축한 블로그이다.

NexT 테마를 사용한다.

## 사용법

1. 이 레포지토리를 클론한다.
1. `_config.in.yml` 파일을 적절히 수정하여 `_config.yml` 파일로 저장한다.
   - Git Deploy 설정을 꼭 확인한다.
1. `themes/next/_config.in.yml` 파일을 적절히 수정하여 `_config.yml` 파일로 저장한다.
   - Algolia 설정을 꼭 확인한다.
1. 다음과 같이 블로그를 실행하거나 배포한다.

   - 다음 명령을 통해 로컬 환경에서 실행할 수 있다.

   ```bash
   npm install

   hexo clean
   hexo generate
   hexo algolia
   hexo server
   ```

   `localhost:4000` 으로 접속하여 블로그를 확인할 수 있다.

   - 다음 명령을 통해 설정한 Git repository에 배포할 수 있다.

   ```bash
   npm install

   hexo clean
   hexo generate
   hexo algolia
   hexo deploy
   ```

   연결된 GitHub Pages 서비스에서 배포된 블로그를 확인할 수 있다.
