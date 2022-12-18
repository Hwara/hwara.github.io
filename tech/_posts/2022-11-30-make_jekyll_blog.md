---
layout: post
title: 'Ubuntu 환경 기반 Jekyll을 이용한 GitHub Blog 구축 및 theme 적용'
date: 2022-11-30 13:34:11
categories: tech
tags: 
- jekyll
- github pages
---

요즈음 배우고 있는 것들은 노션으로 문서화를 열심히 하고 있기에, 이왕 문서화 해둔 거 기술 블로그를 하나 만들어서 공유하면 좋겠다고 생각했다. Jekyll과 GitHub Pages를 이용한 기술 블로그를 많이 쓰는 것 같아 이번에 한 번 만들어 보았다.

## 사전 환경 구성

필자는 기존에 라즈베리파이4 Ubuntu 환경을 구성해놓았기 때문에 [공식사이트](https://jekyllrb-ko.github.io/docs/installation/ubuntu/)에서 Ubuntu를 위한 메뉴얼을 보고 Ruby를 설치하였다.

우선 다음 command를 사용해 최신 버전의 Ruby를 설치한다.

```bash
sudo apt-get install ruby-full build-essential zlib1g-dev
```

jekyll에선 ruby gem의 설치 경로를 루트 계정이 아닌 사용자 계정으로 변경하도록 권고하고 있다.
다음 command로 환경 설정 변수에 설정을 추가할 수 있다.

```bash
echo '# Install Ruby Gems to ~/gems' >>> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

필자는 echo에 해당하는 부분은 직접 ~/.bashrc 파일을 에디터로 열어 추가하였다.

위의 기본 환경 설정이 끝났다면 이제 Jekyll을 설치하면 된다.

## Jekyll 설치 및 구성

1. Jekyll 과 Bundler 젬을 설치

```bash
gem install jekyll bundler
```

2. ```./blog```에 새 Jekyll 사이트를 생성

```bash
jekyll new blog
```

3. 생성된 디렉토리로 이동 후 사이트 빌드 및 로컬 서버에 적용

```bash
cd blog
bundle exec jekyll serve
```

4. 브라우저에서 <http://localhost:4000>에 접속

정상적으로 설치가 됐다면 여기서 jekyll 기본 화면이 보일 것이고, 나는 여기서 에러가 발생했다. 끝에 --trace를 붙여 에러 자세히 확인하라길래 붙여서 확인해보니 다음과 같이 에러가 발생했다.

``` `require': cannot load such file -- webrick (LoadError)```

"webrick이 뭔데 씹덕아" 를 외치고 싶었지만 에러를 뿜으며 울고있는 terminal을 달랠 방법을 찾았다.

다행히도 나같은 사람이 많은지 해결책을 금방 찾을 수 있었다.

### 에러 해결

```bundle exec jekyll serve --trace``` 입력 시 ``` `require': cannot load such file -- webrick (LoadError)```에러가 발생했을 때 에러 해결 방법

```bash
bundle add webrick
bundle install
```

이후 다시 로컬 서버에서 접속해보면 사이트가 잘 열린다!

## GitHub Pages로 호스팅하기

꽤나 어려울 줄 알았는데 설정할 것도 별로 없고 금방 할 수 있도록 잘 만들어놔서 호스팅 자체는 너무 편안했다.

GitHub에서 Repo 이름을 ```your_want_name.github.io``` 와 같은 형식으로 Repo를 만들어준다.
위에 jekyll로 만든 폴더에서 git 설정하고 커밋 후 github과 연결해준다.

```git
git init
git remote add origin https://github.com/user-name/repo-name
git add .
git commit -m "commit message"
git push -u origin main
```

Main Branch가 main이 아니라 master인 경우도 있으니 잘 확인할 것.

repo에 사이트 push까지 잘 해주었다면 이제 github 사이트에서 설정만 해주면 된다.

블로그 Repo에 들어가 탭 끝에 있는 Settings -> 좌측 Code and automation에 있는 Pages로 들어가면 Source와 Branch를 설정할 수 있다.

Source는 Deploy from a branch로 설정하였고, Branch는 자세한 내용은 후술하겠지만 각자 상황에 따라 알맞게 선택한다. 필자는 처음에 main으로 적용했다가 테마 적용 후 gh-pages로 변경하였다.

### Jekyll 테마 적용하기

jekyll 테마는 검색해보면 진짜 셀 수 없이 많다.
유료도 있고 무료도 있고 다양하게 있지만 필자는 무료 테마 중 H20-ac가 가장 깔끔하고 맘에 들어서 이걸로 선택했다.

테마마다 적용방법이 다른지는 모르겠는데, **필자는 기존 jekyll 만들어 둔 폴더에 그대로 다운로드 받은 테마 압축 파일을 풀어 넣고 _config.yml 및 전반적인 페이지 구성 파일들을 수정해주었다.**

테마마다 _config.yml에서 수정하는 내용이 다른거 같은데 보통 블로그의 이름, 닉네임, SNS 링크 등 전반적인 블로그 설정에 관련된 내용들이 많다. 다른글에서 따로 다룰 예정.

전반적인 사이트에 대한 설정을 마치고 테마 내 css, html 파일을 내 입맛에 맞게 바꾸고 위에서 했듯이 로컬서버를 돌려보니 블로그가 내 취향에 맞게 잘 꾸며졌다!

와 이제 글만 쓰면 되겠다 나이스~! 라고 생각하고 기대감에 부푼것도 한 순간, 분명히 로컬 서버에서는 POST 목록이 잘 보이는데 Github pages에 배포하면 보이지 않는것이었다.

이때 로컬 서버에서 성공했다고 Github Pages에 배포했을 때까지 성공한다는 보장은 없다는걸 깨달았다.

### Github pages는 특정 plugins만 지원한다

Github pages에서는 모든 plugin을 지원하는것이 아니라 몇몇개의 plugin만 공식적으로 지원하기 때문에 github pages dependency에 들어가 있는 plugin만 지원이 된다. 절망스럽게도 내가 받은 theme는 jekyll-paginate-v2 라는 녀석이 적용되어 있었으며 github pages는 이를 지원하지 않았다. plugins이 적용되지 않으니, 블로그에 들어가보면 내가 쓴 글들의 목록이 보이지 않았다.

**"그럼 이 안에 없는 plugin들은 못쓰는건가요?!"**
절규하며 해결방법을 모색해보니, github pages 자체 환경에서 build를 할 때 plugin을 적용하지 못할 뿐이지, 내 컴퓨터나 다른 서버 등 **해당 plugin을 지원하는 환경에서 build하고, Github pages에서 나타내도록 해주면 된다**는 것이다!

근데 다른 사람들은 Travis 같은 것들로 자동화하는 방법만 소개하기에 일단 따라했는데 Travis 무료로 사용할랬더니 카드가 올바지지 않다고 안된단다.. 지금까지 너무나 잘 써온 마스터카드인데.. 그래서 포기하고 이유는 알았으니 해결을 하기 위해 삽질을 하기 시작했다.

"그럼 build 결과물인 _site만 넣고 push하면 된다는건가?"
그래서_site를 넣고 push했는데 오.. 안된다.. 지금 생각해보면 _site 넣는다고 서버에서 build를 안하는게 아니니 당연,,^^

뭐가 문제지 싶어 처음엔 설정을 잘못 건드렸나 싶어_config.yml 설정을 계속 바꿔도 보고 paginate-v2에 관련된 옵션을 모두 지워 보기도 하고 여러가지 건드렸는데 POST 페이지는 내 심정을 아는지 모르는지 계속 내게 목록을 보여주지 않았다..

로컬 서버에서는 잘 되는걸 보면_config.yml 설정 문제는 아닐거 같아 Github을 좀 둘러보니 action에서 이것저것 하고 있다는걸 주목하게 됐다. 처음엔 그냥 파일에 문법 에러나 깨진게 없는지 테스트 해주는건가? 싶어 안 건드리고 있었으나 내용을 살펴보니 build도 하고 deploy까지 하는것이 이 친구들이라는 것을 알게 됐다. '잉..? 근데 얘네는 build에 deploy까지 한다는데 build한 결과물을 도대체 어디에 놔두고 뭘 deploy까지 하고 있는거지?' 라는 생각에 찾아보니 내부 서버에서 build하고 해당 build의 결과물을 gh-pages라는 branch에 자동으로 deploy까지 해주고 있었던 것이다.

그래서 좀 더 찾아보니 **github pages에 웹사이트가 배포되는 단계**는 아래와 같다.

1. 글 작성 후 이를 github에 push
2. Github server 환경에서 내가 push한 파일들을 기반으로 Build
3. 해당 생성된 결과물이 gh-pages branch에 배포 및 이 파일을 기반으로 웹 페이지 호스팅

'그렇다면 gh-pages에 내가 build한 결과물을 직접 push하면 post 목록이 멀쩡하게 보이지 않을까?'

두근두근하며 gh-pages에 build해놓은 파일을 push했지만 이마저도 실패.

web쪽으로 build, deploy부터 github actions 등 CI/CD쪽으로 전혀 지식이 없다고 해도 무방할 정도로 아는게 없었기 때문에 이때쯤 멘붕이 왔다.

그렇게 github actions을 어떻게 활용하는건지 구조가 어떻게 돌아가는지 이리저리 삽질도 해보며 대충 파악할 때쯤 근데 왜 무슨짓을 해도 gh-pages에 있는 내용이 사이트에 적용이 안되지?

다시 한 번 절망하고 있는데.. 머리 속에 한 생각이 번뜩 스쳐갔다.

'어라.. 그리고보니 Github repo 설정에 pages에 무슨 branch 고르는거 있지 않았나..? 설마 그건가...??'

여러 블로그를 전전하며 세팅을 배우는게 아니라 그저 따라했을 뿐인 나였기에 Github repo 설정에 문제가 있을 줄은 상상도 못했다.

그렇게 설정을 확인해보니 아니나 다를까 main branch를 기반으로 github pages가 호스팅되고 있는걸 확인했다.

이것만 바꾸면 삽질이 드디어 끝나는 걸까..?

손을 벌벌 떨며 **repo 설정에서 github pages에 호스팅 되는 branch를 gh-pages로 변경**했고 POST 목록은 너무나도 잘 보여 감격의 눈물 한 방울 또르륵 흘렸다 ㅜㅜ..

성공한 방법을 간단히 요약하자면,

1. repo의 github pages 설정에서 branch를 gh-pages로 설정
1. 내 PC에서 글 작성 후 jekyll build
2. _site 폴더에 build된 파일을 github 블로그 repo의 gh-pages에 commit 및 push

참고로 설정에 따라 branch의 이름 변경도 가능하다.

사실 github dependency에 제공하는 plugin만 사용했더라면 이런 귀찮은 일이 없었을터인데..

그래도 어차피 이후에 다른 plugins 쓸려면 했어야 할 일이니까 매도 먼저 맞는게 낫겠지~

삽질하다보면 정말 사소한 곳에서 잘못되어 안되는 경우가 참 많은거 같다. 이번에도 그런 경우일 줄은.. 여튼 성공적으로 테마 적용을 마무리 할 수 있었다.

## 끝난줄 알았지? 개발엔 끝이 없다

하지만 개발자에게 끝이란 건 존재하지 않는다!
톱니바퀴가 되어 영원한 굴레에서 벗어날 수 없는것이 역시 개발자 아니겠습니까. 하하.

일단 post가 보이니 포스팅 자체는 가능해졌지만 글 쓰는건 여전히 불편하기에 여러가지 고쳐야 할 사항이 많다.

현 시점에서 포스팅은 이렇게 하고 있다.

1. main branch에서 _post 폴더에 VSCode로 Markdown 글 작성
2. 상단 헤더에 직접 글 제목, 날짜 추가
3. jekyll Build
4. main branch에서 build한 파일을 gh-pages branch에 push

위 과정을 거치면 포스팅이 완성되는데.. 원시인도 아니고 직접 날짜 하나하나 쓰고 직접 build도 하고~ push도 하고 앉아있으니 자동화가 우리 삶에 얼마나 이로운 것인지 다시 한 번 깨닫는 계기가 되었다.

그래서 Jenkins나 Travis ci를 적용해 자동화 해보고 이를 하는 방법을 다음에 써 볼 생각이다. 언제가 될지는 모르겠지만~

추가로 지금 구상중인 것은 notion api 사용해서 notion에 글 작성해서 특정 notion database에 올리면 자동으로 깃헙 블로그에 배포되도록 하는걸 생각하고 있다.

이전에 notion api를 이용해서 데이터베이스에 있는 글을 가져오거나 글을 쓰는 등 테스트는 깔짝거려봤기 때문에 가능은 할 듯!

갈수록 할 일이 태산이구만~ 좀 더 열심히 해야겠다!

---

# Reference

<https://jekyllrb-ko.github.io/docs/>
<https://supermemi.tistory.com/145>
<https://shlrur.github.io/develog/2019/01/01/jekyll-template-story-3/>
