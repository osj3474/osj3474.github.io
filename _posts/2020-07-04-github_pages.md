---
title : 블로그를 시작하려는 분들을 위한 정리
categories:
   - etc
---

# Github pages

*테마는 가장 기본 테마로 알려진 minimal mistakes 를 이용하여 시작하겠습니다.* 


## 1단계
바탕화면(Desktop)에 새 폴더(blog)를 만들고, 

<img src="https://user-images.githubusercontent.com/42775225/86331308-de079d00-bc83-11ea-9c7d-33de58a1edb1.png" alt="image" style="zoom:33%;" />

   

구글에 minimal mistakes 깃허브 링크를 들어갑니다.

<img src="https://user-images.githubusercontent.com/42775225/86331140-9f71e280-bc83-11ea-8949-c415fe65ab69.png" alt="image " style="zoom:40%;" />





## 2단계
터미널을 이용하여, blog 폴더로 이동한 뒤 minimal mistakes를 클론합니다.

```shell
git clone https://github.com/mmistakes/minimal-mistakes.git .
```

(원래 클론하면, 클론한 위치에서 폴더가 새롭게 생기게 되는데, 그렇게 하지 말고, '.' 을 추가하면, 현재 폴더가 git 폴더가 되게 합니다.)

cf) 클론할 링크

<img src="https://user-images.githubusercontent.com/42775225/86332116-03e17180-bc85-11ea-9e72-b470f436bcc1.png" alt="image" style="zoom:50%;" />





## 3단계 

다시 github로 가서 Repository를 생성합니다. 이 때, 

```shell
계정명.github.io
```

으로 합니다.

<img src="https://user-images.githubusercontent.com/42775225/86329271-deeaff80-bc80-11ea-909d-098be9b9b99f.png" alt="image " style="zoom:35%;" />





## 4단계

터미널을 이용해서 blog 폴더로 이동하고, 다음의 명령어를 입력합니다. 아래의 작업은 다른 사람의 Repository가 아닌, 우리의 Repository와 로컬의 폴더와 연결하는 작업입니다. (origin이라는 이름으로 Repository와 연결)

```shell
cd
cd Desktop/blog
sudo rm -r .git
git init
git remote add origin [아래 클릭한 링크]
```

![image](https://user-images.githubusercontent.com/42775225/86333178-84ed3880-bc86-11ea-8ba8-820b15e6bc25.png)



## 5단계 

이제 blog 폴더 안에 있는 모든 파일에 대하여 버전관리를 시작합니다. 

```shell
git add .
git commit -m 'blog start'
git push origin master
```

각각의 줄은 다음을 의미합니다.

1) 모든 폴더를 스테이지에 올린다.

2) 간단한 설명(-message)과 함께 커밋한다.

3) 원격 저장소 origin에 내 로컬 master branch 로 업데이트 한다.





## 6단계 

그러면 일단 페이지가 뜨는 것을 확인할 수 있습니다. 웹 브라우저에 

```
https://osj3474.github.io/
```

와 같이 '계정명.github.io' 를 입력합니다.



<img src="https://user-images.githubusercontent.com/42775225/86428687-53c54480-bd28-11ea-94a7-881c1df3ac2d.png" alt="image " style="zoom:40%;" />

## 7단계 

이제부터 커스터마이징입니다. 우선, Jekyll의 기본 요소에 대해 숙지합니다.

- _config.yml : 설정 파일
- _posts : 블로그 포스팅
- _pages : 개별 페이지
- _includes : 글에 포함되는 개별 요소
- _layouts : 글의 양식
- assets : image, css 등
- index.html : 표지

너무 상세하게 알면 머리 아프니까, 최소한으로 해야하는 것만 다룹니다. 

우선, blog 폴더 안에서 ' _posts ' 라는 이름의 폴더와   ' _pages ' 라는 이름의 폴더를 만듭니다.

<img src="https://user-images.githubusercontent.com/42775225/86442193-59cb1d80-bd48-11ea-986a-378e0de26bec.png" alt="image " style="zoom:50%;" />

그리고, _posts 폴더 안에, 저희가 올리고 싶은 글을 markdown 파일로 올립니다. 파일 명의 형식은 다음의 형식을 따라야 합니다.

<u>***년-월-일-파일명.md***</u>

```
2020-07-03-first_posting.md
```

안에 들어가는 내용은

```markdown
---
title : "블로그를 시작하기로 마음 먹은 날"
---

## 완벽함을 추구하려 했으나

내용은 다음과 같습니다.
```

   



## 8단계 

그 다음은 _config.yaml 파일을 수정할텐데요.

https://mmistakes.github.io/minimal-mistakes/docs/configuration/

위 링크를 설명서처럼 사용하시면 됩니다. 몇가지만 같이 짚고 가겠습니다.



```yaml
# Site Settings
locale                   : "ko-KR"       # primary language는 Korea
title                    : "차곡차곡"
name                     : "Sangjin Oh"
description              : "980번 연속 패를 당해도 결국 우린 이길 수 있어"
url                      : "https://osj3474.github.io"
search                   : true   # 이걸 해야 검색 돋보기가 생겨요

# Site Author
author:
  name             : "Sangjin Oh"
  avatar           : "/assets/images/sangjin.jpg"     # asset폴더 안에 images폴더를 만들어서 이미지를 넣어요
  bio              : "I realize the value of data."
  location         : "Pohang, Republic of Korea"
  email            : "21500404@handong.edu"
  links:
  - label: "GitHub"
    icon: "fab fa-fw fa-github"
    url: "https://github.com/osj3474"
```



## 9단계 

_data 폴더에서 navigation.yml 를 수정합니다. 맨 상단에 메뉴버튼이며, 페이지 이동에 사용하면 좋을것 같습니다.

```yaml
# main links
main:
  - title: "Manual"
    url: https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/
  - title: "menuA"
    # url: https://mmistakes.github.io/minimal-mistakes/about/
  - title: "menuB"
    # url: /year-archive/
  - title: "menuC" 
    # url: /collection-archive/
  - title: "menuD"
    # url: /sitemap/
```



​	여기까지 완료하면 다음과 같은 사이트가 됩니다.

​	![image](https://user-images.githubusercontent.com/42775225/86442835-571cf800-bd49-11ea-9012-2560dab85e5f.png)	

## 10단계 

끝으로 github page를 사용하기 위해 이해한 내용을 영상으로 정리해보았습니다. (클릭하시면 바로 유투브로 넘어가니 당황하지 마시고.. :))

* <a href="https://www.youtube.com/watch?v=G-8A1w-YD_k&feature=youtu.be" target="_blank"> 내가 이해한 git clone </a>

* <a href="https://www.youtube.com/watch?v=Dz9SGB7kO4c&t=218s" target="_blank"> 내가 이해한 github pages  </a>



