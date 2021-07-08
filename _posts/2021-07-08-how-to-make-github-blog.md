---
layout: post
title: "깃허브 블로그 만들기(feat.공식문서)"
date: 2021-07-08
categories:
  - Github Pages
tags:
  - github pages
  - static page generator
  - jekyll
author: HoHyeong Kang
---



# 0. 서론

이 블로그를 개설하기 이전에도 여러번 깃허브 블로그 만들기를 시도한 적이 있었습니다. 가장 많이 썼던 방법은 오픈소스 소프트웨어 실습 수업에서 배운 방법으로

1. Jekyll 테마를 고른다.
2. 해당 테마를 포크해온다.
3. config 파일과 몇 가지 저장소 설정을 수정해서 내 것으로 만든다!

라는 간단하고 빠른 방법이었죠. 그러나 이런 방법으로 블로그를 뚝딱 만들고 나면 굳이 필요하지 않거나 의미를 알 수 없는 파일들도 통채로 가져오기 때문에 내 블로그인데도 통제되지 않는 요소가 있다는 스트레스가 생기고 그로 인해 내 것이라는 주인의식이 희미해졌습니다. 또한 레이아웃이 복잡하고 문서화가 잘 되어있지 않은 테마일수록 원하는대로 커스터마이징이 되지 않으니 블로그에 애정이 생기질 않아서 쉽게 포기해버리게 되었습니다. *(물론 이것은 저 개인적인 문제이고 위와 같은 방법으로도 충분히 멋진 블로그를 만들 수 있습니다.)*

그래서 이번에는 아예 각잡고 [깃허브 공식문서](https://docs.github.com/en/pages)를 따라가면서 완전히 내 것인 블로그를 만들고자 하였습니다. 본 포스트는 제가 따른 GitHub Docs의 GitHub Pages 관련 내용에 대한 요약 및 번역, 거기에 제가 읽으면서 추가적으로 이해가 필요했던 부분을 추가한 내용으로 구성하였습니다. 최종적으로 **언제든지 외형과 기능을 추가, 제거, 수정**할 수 있고, **로컬에서 테스트 후 퍼블리싱**할 수 있는 형태의 블로그 제작을 목표로 합니다.



>## 목차
>
>1. [GitHub Pages 란?](#1-github-pages-란)
>2. [GitHub Pages의 특징](#2-github-pages의-특징)
>3. [GitHub Pages 만들기](#3-github-pages-만들기)
>4. [Jekyll의 특징](#4-jekyll의-특징)
>5. [Jekyll로 GitHub Pages 만들기](#5-jekyll-로-github-pages-만들기)
>6. [Jekyll로 블로그 운영하기](#6-jekyll로-블로그-운영하기)

---



# 1. GitHub Pages 란?

GitHub Pages 는 깃허브에서 제공하는 정적 사이트 호스팅 서비스입니다. 정적 사이트란 동적 사이트와 반대되는 용어로 서버에 저장된 컨탠츠를 그대로 모든 사용자에게 동일하게 보여주는 사이트입니다. 즉 GitHub Pages 는 **GitHub의 저장소 기능을 활용하여 저장소에 저장된 정적 파일들을 호스팅해주는 서비스**라고 할 수 있습니다.

​    

---



# 2. GitHub Pages의 특징

### - 기본 도메인(github.io)을 제공한다

GitHub Pages를 사용해 퍼블리싱한다면 **github.io 도메인을 무료로 사용**할 수 있습니다. 물론 개인 도메인을 설정하여 사용할 수도 있습니다.

### - 개인별, 또는 프로젝트별로 사이트를 만들 수 있다

공식문서에서는 GitHub Pages로 만들 수 있는 사이트의 타입을 `project`, `user`, `organization` 세 가지로 구분하고 있지만 역할과 설정의 차이를 고려할 때 `user`와 `organization`은 큰 차이가 없어 크게 `user`과 `project`로 나눌 수 있습니다. 이 둘의 차이는 다음과 같습니다.

- `user` 사이트는 개인 당 오직 하나만 생성 가능하다. 반드시 저장소 이름을 \<username\>.github.io 로 생성해야하며 해당 url로 사이트에 접근하게 됩니다. 
- `project` 사이트는 여러 개의 프로젝트에 대해 각각의 사이트를 만들 수 있습니다. 주의해야 할 점은 커스텀 도메인을 사용하지 않는다면 프로젝트 사이트의 주소가 \<username\>.github.io/\<repository\> 로 할당되기 때문에 개인 사이트와 URL 앞부분을 공유한다는 것입니다.
- 또한 `project` 사이트를 위한 소스파일은 해당 프로젝트의 저장소에 함께 보관되기 때문에 기본적으로 `gh-pages`라는 이름의 별도의 브랜치에 정적 파일들을 보관하고 해당 파일들을 퍼블리싱하게 됩니다. *(개인, 프로젝트 사이트 모두 루트로 사용할 브랜치와 경로를 따로 설정할 수 있습니다.)*

### - 기본적으로 Jekyll을 사용하여 빌드한다

GitHub Pages는 설정된 경로에 저장된 모든 정적 파일들을 퍼블리싱합니다. 따라서 어떤 방식으로든 정적 파일을 만들어 저장소에 저장하기만 하면 자동으로 퍼블리싱됩니다. 그러나 GitHub Pages는 기본적으로 Jekyll을 사용하여 정적 파일들을 빌드하고 퍼블리싱하는 프로세스를 가지고 있습니다. *(Jekyll에 대한 조금 더 자세한 내용은 후술하겠습니다.)*

- Jekyll은 GitHub 공동 설립자가 제작한 **static site generator** 입니다. 정적 사이트 생성기는 말 그대로 손쉽게 정적 사이트를 구축할 수 있도록 도와주는 도구입니다. Jekyll 말고도 여러 정적 사이트 생성기가 존재하므로 굳이 Jekyll을 사용할 필요는 없습니다.
- 그러나 Jekyll을 사용하지 않으면 GitHub Pages의 기본적인 빌드 프로세스와 충돌할 가능성이 있으므로 `.nojekyll` 파일을 추가하여 자동 빌드 프로세스를 중단시켜야 합니다.
- 당연하게도 서버사이드 언어(php, ruby, python 등)은 지원하지 않습니다.

### - 제한 사항

- 저장소의 저장용량은 1GB로 제한됩니다.
- 퍼블리싱된 사이트의 크기도 1GB를 넘을 수 없습니다.
- 월간 약 100GB의 대역폭 제한과 시간당 약 10번의 빌드 제한이 있습니다.
- GitHub Pages를 통해 비밀번호, 계좌 정보 등을 다루는 것은 바람직하지 않으며, GitHub Pages를 비즈니스, 또는 상업용으로 활용하는 것은 금지되어있습니다.

​    

---



# 3. GitHub Pages 만들기

GitHub Pages를 만드는 방법은 아주아주아주 쉽습니다. 다음은 GitHub Pages를 통해 `user` 사이트를 만드는 거의 모든 경우*(Jekyll을 사용하지 않더라도)*에 적용할 수 있는 일반적인 방법입니다. 

1. 새 저장소를 생성합니다. 이때 저장소 이름은 반드시 **\<username\>.github.io** 로 설정합니다.
2. 해당 저장소의 Settings -> Pages 탭으로 이동 후 정적 파일들을 저장할 Source 브랜치 및 경로를 설정합니다.  ![settings](\images\2021-07-08\settings.png)  
3. 해당 경로에 정적 파일을 추가합니다. (ex. index.html) 이때 저장소 소스의 디렉토리 구조와 동일하게 사이트에 접근할 수 있습니다. 예를 들어, 설정한 루트 경로에 `post` 폴더를 만들고 그 안에 `hello.md` 를 추가하였다면 해당 파일은 `https://<username>.github.io/post/hello.html `로 접근할 수 있습니다.
4. 끝

어떤 방법을 사용하더라도 기본원리는 위와 동일합니다. 다음으로는 GitHub Pages의 내장 정적 사이트 생성기인 Jekyll을 활용하여 사이트를 구성하는 방법을 살펴보겠습니다.

​    

---



# 4. Jekyll의 특징

앞서 설명드린바와 같이 Jekyll은 가장 유명한 **static site generator** 중 하나입니다. 만약 정적 사이트 생성기를 사용하지 않고 운영자가 직접 꾸준히 포스팅해야 하는 정적 사이트를 운영한다면 직접 모든 레이아웃별 HTML, CSS, JS 파일을 만들어야 하고 새로운 포스팅을 할 때마다 새로운 HTML 파일을 만들어서 추가해야 하는 등 상당히 번거롭고 반복적인 일을 해야할 것 입니다. 하지만 정적 사이트 생성기를 사용한다면 **다른 사람들이 잘 만들어 놓은 레이아웃과 기타 도구들을 간단히 불러와서 사용**할 수 있을 뿐만 아니라 아주 쉬운 **마크다운 문법으로 적은 문서를 추가하는 것 만으로 간단하게 포스팅** 할 수 있습니다. 다음은 Jekyll의 대표적인 특징들 입니다.

- `마크다운`과 `Liquid`라는 템플릿 언어를 지원합니다. `마크다운`은 복잡한 태그 구조 없이 HTML 파일을 작성할 수 있게 해주고, `Liquid` 는 HTML에서 변수나 제어, 반복문을 사용할 수 있게 해줍니다.
- 데이터베이스를 사용하지 않습니다. 만약 지속적으로 사용되어야하는 데이터(ex 작성자 정보) 가 있다면 YAML, JSON 과 같은 데이터 파일을 활용할 수 있습니다. 해당 데이터는 Liquid 문법을 통해 접근할 수 있습니다.
- **GitHub에 내장되어 있습니다!** GitHub Pages는 퍼블리싱 이전에 기본적으로 Jekyll 빌드 프로세스를 진행하므로 Jekyll을 사용한다면 빌드하는 과정 없이 파일들을 저장소에 올려놓는 것만으로 퍼블리싱이 가능합니다.

​    

---



# 5. Jekyll 로 GitHub Pages 만들기

Jekyll 을 사용하여 GitHub Pages를 만드는 방법은 크게 세 가지로 나눌 수 있습니다.

1. GitHub의 Theme Chooser 기능 사용하기
2. 오픈 소스 Jekyll 테마를 fork 또는 clone하여 사용하기
3. **로컬에 Jekyll 설치 후 Git 으로 연동하기**

1번의 경우 매우 간단하지만 GitHub의 [supported themes](https://pages.github.com/themes/)만 사용할 수 있으므로 (제가 느끼기에는) 예쁜 테마가 없다는 것이 단점입니다. ([Theme Chooser 사용하기](https://docs.github.com/en/pages/getting-started-with-github-pages/adding-a-theme-to-your-github-pages-site-with-the-theme-chooser#adding-a-theme-with-the-theme-chooser))

2번의 경우 서론에서 말한 바와 같이 내 블로그를 만든다는 느낌보다는 남의 집에 세를 들어 사는 느낌(?)이 나고 무엇보다도 1번과 2번 모두 **로컬에서 실제 사이트에서 보이는 포스트의 모습을 확인할 수가 없다는 치명적인 단점**이 있습니다.~~*(물론 다양한 방법으로 극복 가능한 단점입니다)*~~

따라서 저는 **로컬에서 마음껏 여러 설정과 컨텐츠를 테스트**하고 **완성된 부분만 커밋하여 배포**할 수 있는 3번 방법을 선택하였습니다. 다음은 3번 방법으로 GitHub Pages를 만드는 과정입니다.([공식 문서](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll))

- 준비물 : 로컬에 설치된 `Jekyll`과 `Git`(+ 의존성 관리도구 `Bundler`)

Jekyll을 설치하기 위해선 Ruby가 필요합니다. 자세한 내용은 [Jekyll 설치법](https://jekyllrb.com/docs/installation/) 을 참고하세요. 간단히 설명하자면 Ruby는 프로그래밍 언어이고 gem 이라는 방식으로 라이브러리를 배포하며 Jekyll은 그러한 gem 들 중 하나입니다. 마찬가지로 gem 방식으로 배포되는 다양한 Jekyll 플러그인들을 다운받아 활용할 수 있습니다. Windows의 경우 Jekyll 설치 및 사용이 가능은 하지만 공식적으로 지원되는 플랫폼이 아니므로 참고바랍니다.*(Windows 10 사용자라면 WSL2 를 통해 설치하는 것을 추천드립니다.)* 다음 설명은 로컬에 Jekyll과 Bundler, Git이 설치되어 있고 Git 사용법을 안다는 전제 하에 기술하겠습니다.

1. 원하는 로컬 폴더에서 git을 초기화(`git init`) 합니다. 

2. 해당 폴더에서 `jekyll new` 명령어 를 통해 새로운 Jekyll 사이트를 생성합니다. 그 결과 다음과 같은 파일들이 생성됩니다. 

   ![jekyll-new](\images\2021-07-08\jekyll-new.png){: .align-center}

3. `Gemfile`을 열어 다음과 같이 수정합니다.

   1. `gem "jekyll"` 라인을 찾아 앞에 '#' 을 붙여 주석화합니다.
   2. `# gem "github-pages"` 라인을 찾아 앞에 '#' 을 지우고 `gem "github-pages", "~> GITHUB-PAGES-VERSION", group: :jekyll_plugins` 로 수정합니다. *GITHUB-PAGES-VERSION* 은 "[Dependency versions](https://pages.github.com/versions/)"을 참고하여 최신 지원 버전으로 적어줍니다. *(테스트 결과 `"~> GITHUB-PAGES-VERSION"` 부분은 생략해도 문제없었지만 의존성 관리를 위해 명시하는 것을 추천드립니다.)*

4. `Gemfile`을 저장 후 닫고 명령줄에서 `bundle update` 명령어를 실행합니다.

5. `_config.yml` 파일을 열어 필요한 부분을 수정합니다. 대표적으로 다음과 같은 부분을 수정하여야 합니다.

	```yaml
	domain: <username>.github.io
	url: https://<username>.github.io
	baseurl: 소스 위치를 루트 경로가 아닌 곳으로 설정하였다면 추가
	```
6. 변경 사항을 커밋 후 원격 저장소에 push 합니다.
7. [전술 한 바](#3-github-pages-만들기) 와 같이 GitHub Pages 활성화를 위한 저장소 설정을 해줍니다.
8. 끝

위와 같이 한 후 `<username>.github.io` 로 접속하여 다음과 같은 화면이 보인다면 성공입니다.

![jekyll-main](\images\2021-07-08\jekyll-main.png)

​    

---



# 6. Jekyll로 블로그 운영하기

향후 블로그 운영, 관리법을 요약하면 다음과 같습니다.

1. 테마, 플러그인 등을 받아 블로그에 적용한다.
2. 블로그 컨텐츠를 추가한다. (page, post 등)
3. 변경 사항을 로컬에서 테스트한다.
4. 테스트 결과  문제 없다면 커밋 후 저장소에 push 한다. (일단 push 되면 빌드 및 퍼블리싱은 GitHub Pages를 통해 자동으로 진행된다.)
5. 반복

각 단계를 세부적으로 살펴보겠습니다.

### - 테마, 플러그인 등을 받아 블로그에 적용하기

다른 분들이 만들어놓은 수많은 Jekyll 테마들은 [이곳](https://github.com/topics/jekyll-theme)을 포함하여 다양한 경로를 통해 구할 수 있습니다. 원하는 테마가 GitHub [supported themes](https://pages.github.com/themes/) 라면 단순히 `_config.yml` 파일의 `theme: ` 부분을 수정하는 것으로 테마 변경이 가능하지만, 그 외 오픈소스 Jekyll 테마라면 `theme: ` 부분을 지우고 `remote-theme: ` 부분에 해당 테마 저장소를 기입하여야 하고, 약간의 추가적인 설정이 필요할 수 있습니다. 각 테마마다 설정해야 하는 것과 할 수 있는 기능들이 제각각 다르지만 대부분의 경우 **해당 테마의 저장소에 설치법이 자세히 기술**되어 있으므로 잘 읽고 따라하면 됩니다. 특히 Jekyll로 만든 사이트의 기본적이고 보편적인 **[디렉토리 구조](https://jekyllrb.com/docs/structure/)**를 살펴본다면 더욱 디테일하게 커스터마이징이 가능합니다. 간략하게 중요한 디렉토리 구조만 살펴보자면 다음과 같습니다.

- `_includes` 폴더에는 보통 footer, header, sidebar 와 같이 HTML 구성 시 빈번하게 추가될 수 있는 요소들이 들어있습니다.
- `_layouts` 폴더에는 보통 사전 정의된 포스트 템플릿이 저장되어 있습니다.
- `_posts` 폴더에는 포스트들이 들어있습니다.
- `_data` 폴더에는 여러 데이터 파일(yml, json, csv 등)이 저장되어 있으며 작성자 정보와 같이 빈번하게 사용되는 데이터 등을 보관할 수 있습니다.
- `_sass` 폴더에는 css 파일이 들어있습니다.

플러그인 설치 시에도 마찬가지로 필요한 플러그인을 찾은 뒤 공식 문서를 잘 따라서 설치하면 됩니다.

### - 블로그에 컨텐츠 추가하기

Jekyll 에서는 블로그의 컨텐츠를 크게 두 가지로 분류합니다.

- `page` 란, 작성한 날짜와 무관한 단독적인 컨텐츠 입니다(ex about 페이지). 독립적인 게시글이나 기능적인 페이지(ex 검색 페이지)를 구성하기 위해 주로 사용됩니다.
- `post` 란, 일반적인 블로그 포스트입니다. Jekyll 의 포스트는 `_posts` 폴더 안에 보관되며 파일명이 `YYYY-MM-DD-TITLE.md` 의 포멧을 따라야 하는 규칙이 있습니다.

굳이 일반적인 `page` 와 `post` 의 역할을 따르지 않고 자유롭게 활용하여도 됩니다. 

또한 Jekyll에는 `Front Matter` 라는 것이 존재합니다. `Front Matter` 는 파일의 맨 처음 정의되는 것으로 특정 파일이 `Front Matter`  블록을 포함하고 있다면 Jekyll 은 그것을 처리할 것입니다. `Front Matter` 는 다음과 같이 생겼습니다.

```yaml
---
layout: post
title: My first post!
---
```

이를 통해 사전정의된 변수와 설정 등을 `page` 와 `post` 에 적용할 수 있습니다. 각 테마별로, 그리고 레이아웃별로 `Front Matter` 를 통해 설정 가능한 변수가 상이하므로 해당 테마의 문서를 잘 확인하시기 바랍니다.

### - 로컬에서 변경사항 확인하기

본 포스트에서 소개한 [로컬에 Jekyll 설치법](#5-jekyll-로-github-pages-만들기) 을 따랐다는 가정하에 기술하겠습니다. 이 방법을 통해 테마를 변경하였거나, 새로운 포스트를 작성하였을때, 퍼블리싱 이전에 실제로 내 블로그에서는 어떻게 보이는지 확인할 수 있습니다.

1. 만약 새로운 Gem 을 추가하거나 수정하였을 경우 `bundle install`  명령어를 통해 변경사항을 적용
2. `bundle exec jekyll serve` 명령어를 통해 변경사항을 빌드하고 로컬 웹 서버를 실행
3. `http://localhost:4000` 주소로 접속하여 내 블로그를 확인

위와 같은 방법으로 실행한 로컬 웹 서버는 내 블로그에 **변경 사항이 있을때마다 사이트를 rebuilding** 해주기 때문에 켜놓고 수정하면 실시간으로 변경 사항을 확인하면서 작업할 수 있습니다.

이렇게 로컬에서 확인 후 문제점이 없다면 커밋 후 원격 저장소에 push 하여 실제 블로그에 변경 사항을 적용하면 됩니다.

​    

---

# 7. 마무리

이 포스트의 내용은 [깃허브 공식문서](https://docs.github.com/en/pages) 의 내용과 대동소이하며 이해를 돕기위한 추가적인 설명과 [Jekyll 공식문서](https://jekyllrb.com/docs/) 에서 발췌한 내용이 추가된 것입니다. 저의 경우엔 이 블로그를 만들기 이전에 몇 번의 삽질 경험이 있어서 얼추 Jekyll 의 특징과 구조에 대해 알고 있었던 것이 수월하게 블로그를 구축하는데 큰 도움이 되었던 것 같습니다. 역시 개발자에게 의미없는 삽질은 없다는 교훈과 더불어 이전에는 얼추 알고 넘어갔던 모호한 지식들을 명확히 하였다는 뿌듯함을 느끼며 포스팅을 마치겠습니다. 감사합니다!

