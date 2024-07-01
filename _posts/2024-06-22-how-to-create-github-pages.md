---
title: Jekyll Chirpy로 Github Pages 만들기
description: Jekyll Chirpy 테마를 사용하여 깃허브 블로그를 생성하는 과정을 담았습니다.
categories: [Etc, Github Pages]
tags: [github pages]
date: 2024-06-22 10:16:00 +0900
---

<hr/>
> 해당 포스트는 MacOS 기준 Chirpy 테마를 사용하였다.<br/>
다른 테마를 사용하고 싶다면 [http://jekyllthemes.org](http://jekyllthemes.org)에서 템플릿을 골라 사용하면 된다.

> 공식 사이트에서 제안한 Chirpy 테마를 사용해 깃허브 페이지를 생성하는 방법은 두가지가 있다.
1. [Chirpy Starter](https://github.com/cotes2020/chirpy-starter)
2. [Github Fork](https://github.com/cotes2020/jekyll-theme-chirpy)<br/>
나는 2번 방법이 더 커스텀이 유용하기 때문에 2번 방법을 이용했다.
<hr/>

## GitHub Fork
먼저 Github에 로그인 한 후 [Github Fork](https://github.com/cotes2020/jekyll-theme-chirpy)에 접속해 `[USERNAME].github.io`(mooniswan.github.io) 이름으로 해당 리포지토리를 fork 한다.<br/>
![](/assets/img/fork.png){: .shadow }

fork한 내 리포지토리에서 web URL을 복사한다.
![](/assets/img/clone.png){: .shadow }

로컬 머신에서 원하는 디렉토리에 복사한 HTTPS 주소를 clone해준다.<br/>
나의 경우 따로 생성하지 않고 iterm에 들어가 바로 명령어를 입력해주었다.
```zsh
git clone [복사한url주소]
```
{: .nolineno }
- (git clone https://github.com/mooni...)

## Node.js 확인
Node.js가 설치되어 있는지 확인한다.
```zsh
node -v
```
{: .nolineno }

- 설치되어 있지 않으면 아래 명령어로 설치를 진행한다
```zsh
brew install node
```
{: .nolineno }

## 루트 디렉토리 이동
클론한 리포지토리의 루트 디렉토리로 이동한다.
```zsh
cd [유저이름].github.io
```
{: .nolineno }

## Tools 초기화
코드를 최신태그로 체크아웃해주고, 샘플 파일 제거, CSS/JS 파일 빌드, 변경 사항 저장과 같은 작업을 수행하는 명령어를 실행한다.
```zsh
bash tools/init.sh
```
{: .nolineno }

## Dependencies 설치
루트 디렉토리에서 아래 코드를 실행하여 dependencies를 설치한다.
```zsh
bundle
```
{: .nolineno }

## _config.yml 업데이트
_config.yml 파일의 변수를 업데이트한다.<br/>

```zsh
vi _config.yml
``` 
- 위 명령어 실행 후 i 클릭 -> 파일 수정 -> Esc키 클릭 -> :wq 로 저장 후 종료 또는 <br/>

```zsh
code .
``` 
- 위 명령어를 통해 직접적으로 폴더에 들어가 vsc를 이용해 편집 후 저장해도 된다.
<br/>

내용은 `'url'`부분은 필수적으로 자신의 블로그 url로 작성해주고 나머지는 기호에 맞게 파일의 주석을 참고해 수정하면 된다.

>timezone: Asia/Seoul<br/>
title: 원하는 이름<br/>
tagline: 이름 밑에 나오는 설명<br/>
url: "github page 주소"<br/>
avatar: '/assets/img/favicons/android-chrome-192x192.png'

- 블로그 왼쪽 상단에 뜨는 avatar를 설정하기 위해서는 [Real Favicon Generator](https://realfavicongenerator.net) 링크에 들어가 
1. `Select your Favicon image` 
2. 원하는 이미지 업로드
3. 맨 아래로 스크롤 후 `Generate your Favicons and HTML code`
4. `Favicon packages` 다운로드
4. assets/img/favicons 에 들어가 browserconfig.xml, site.webmanifest 삭제 후 다운로드 파일 내용으로 덮어쓰기 해주면 된다.

## 로컬 서버 실행
로컬 서버를 실행하여 설정된 페이지를 확인한다.
```zsh
bundle exec jekyll s
``` 
{: .nolineno }

## 배포
잠금파일의 플랫폼 목록을 업데이트한다.
```zsh
bundle lock --add-platform x86_64-linux
```
{: .nolineno }

## Settings 변경
Settings 탭의 Pages에 들어가 Build and deployment 부분의 Source를 `GitHub Actions`으로 바꿔준다.
![](/assets/img/source.png){: .shadow }

아래 명령어를 사용해 commit을 push해주면 자동으로 Build and Deploy가 완료된다.
```zsh
git add .
git commit -m 'test'
git push
```
{: .nolineno }

## Build 오류 해결
위 푸쉬과정 중 Github Actions 탭에서 확인한 결과 build 과정에서 에러가 발생해 사이트가 배포가 안되는 걸 확인했다.
Test site 과정에서 _site 폴더의 html 파일에서<br/>
<span style="color:red"> internal script reference /app.min.js does not exist </span><br/>
위와 같은 오류내용때문에 빌드가 실패되었다.

어차피 게시물을 작성하고 로컬서버에서 확인 후 최종본을 배포하기 때문에 <br/>
test site 부분이 필요없다고 가정하고 .github/workflows 로 이동해 `pages-deploy.yml`, `ci.yml` 파일에서 Test site 과정을 아예 주석처리 해서 오류를 해결했다. 

![](/assets/img/img2.png){: .shadow }

commit changes -> sign off and commit changes를 통해 저장해주면 된다. 
![](/assets/img/img1.png){: .shadow }

이후 다시 push 과정을 진행됐더니 성공적으로 빌드 및 배포가 완료됐다.<br/><br/>
글을 쓸 때는 _posts 폴더에 `YYYY-MM-DD-TITLE.md` 양식을 지켜서 마크다운 문법으로 저장한 파일을 아래 명령어로 로컬서버에서 확인 후 최종본을 push해주면 된다.
```zsh
bundle exec jekyll s
``` 
{: .nolineno }

### 참고 사이트
***
* 공식 Chirpy 사이트: [https://chirpy.cotes.page/posts/getting-started/](https://chirpy.cotes.page/posts/getting-started/)
* 하얀눈길 블로그: [https://www.irgroup.org/posts/jekyll-chirpy/](https://www.irgroup.org/posts/jekyll-chirpy/)