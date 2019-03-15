---
layout: post
title:  "Hello Docker"
date:   2019-02-07 15:00:19 +0900
categories: Web
tags: Docker Web Django
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

# 한영님과 함께하는 Docker 기초!

### 참고 문서

- [초보를 위한 도커 안내서](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)
- [친절한 한영씨 - git](https://github.com/LeeHanYeong/DockerExample)
- [친절한 한영씨 - youtube 영상](https://docs.google.com/spreadsheets/d/1SQcvzFnOLO6QU1wYD2kLub91jkDEyy5fWHVctOCHb8E/edit#gid=0)

도커에 대한 개념은 첫 번째 링크가 아주 잘 정리해주었다 :) 나는 실습위주로 정리.

## 프로그램 설치

### Python 설치 확인

```shell
$ brew list
$ python3 --version
```

python 은 기본적으로 python version 2 를 말하고 python3 는 version 3를 말한다!

python에서 소프트웨어 관리하는 패키지 관리 시스템인 pip(Python Package Index)도 pip 는 version 2, pip3 는 version 3 를 말한다.

### Django 프로젝트 설치

 [Get started with Docker Desktop for mac](https://docs.docker.com/docker-for-mac/) 에 보면 nginx 기반의 boilerplate 프로젝트가 있는 것 같지만 Django 해보고 싶으니까 Django 설치해보자!

* [Django 설치](https://tutorial.djangogirls.org/ko/django_installation/)

### requirements.txt 만들기

얘는 왜 만들어보냐면 도커가 개발환경에 필요한 패키지들을 묶어서 이미지화 하고 그걸 이용해서 동일한 환경을 복제하기 쉽도록 만들어주는데 이미 이전부터 pip3 freeze / install 을 이용하면 패키지 목록을 파일에 저장하고 이 파일을 이용해서 패키지들을 재설치 할 수 있었다. 이런게 있었다 + 뒤에 가서 쓸거다.

* **pip3 list** : 현재 환경에 설치된 패키지 목록 조회
* **pip3 freeze** : 현재 환경 내에 설치된 패키지 목록을 특정 파일에 기록
* **pip3 install** : 파일에 저장된 패키지 목록을 설치

```shell
$ pip3 list
$ pip3 freeze > requirements.txt
$ cat requirements.txt
Django==2.1.5
pytz==2018.9

$ pip3 install requirements.txt
```

### Docker Desktop 설치

- [Install Docker Desktop for Mac](https://docs.docker.com/docker-for-mac/install/)

## Django 프로젝트 로컬 서버로 올리기

### 프로젝트 생성

```shell
$ django-admin startproject blog
```

위 명령어로 쟝고 템플릿 프로젝트를 생성할 수 있다. 프로젝트 이름이 blog!

만들어진 프로젝트는 기본적으로 다음과 같은 구조를 가진다.

```
blog
├── manage.py
└── blog
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

### Django 프로젝트 서버 올려보기

blog 프로젝트에 기본적으로 생성되는  manage.py 파일을 통해 Django 프로젝트를 서버에 올릴 수 있다.

```shell
$ python3 blog/manage.py runserver
Performing system checks...

System check identified no issues (0 silenced).

You have 15 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

February 07, 2019 - 04:05:00
Django version 2.1.5, using settings 'blog.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

ip 주소와 포트를 설정해주지 않으면 기본적으로 127.0.0.1:8000 으로 연결된다. **python [name]/manage.py runserver [ip:port_number]** 로 설정해줄 수 있다.

브라우저를 켜고 localhost:8000 으로 접속하면 쟝고 프로젝트의 개발버전(디폴트)이 올라간걸 알 수 있다!

### 화면 추가하기

Django에 화면을 추가하려면 화면을 구성할 .py 파일을 만들고 이를 urls.py 에 등록해주어야 한다.

**views.py**

```python
from django.http.response import HttpResponse

def index(request):
	return HttpResponse('<h1>Hello Django!</h1>')
```

이미 만들어져 있는 **urls.py** 파일에 우리가 만든 **views.py**파일에서 정의한 index 를 import 하고 url patterns 에 추가한다.

**urls.py**

```python
from django.contrib import admin
from django.urls import path

from .views import index

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', index),
]
```

쟝고 프로젝트에 대한 추가적인 이해는 [Django Documentation - Tutorial](https://docs.djangoproject.com/ko/2.1/intro/tutorial01/) 을 참고하자!!!

이제 서버를 다시 올리면 (서버를 내리려면 Control+C) 아까의 로켓은 없어지고 Hello Django! 가 나온당! 우왕.

## Docker 사용해보기

### Docker Image 사용하여 서버 올리기

자 이제 이걸 Docker 를 사용해서 해보자. 우리는 이미 로컬에 python을 깔았지만, Docker를 통해 가상의 python 환경을 만들어내고 그 위에 서버를 띄워보자.

이미 많은 OS의 Docker Image들이 존재한다. 우리가 사용할 python 공식 Image는 [Docker Hub](https://hub.docker.com/_/python/) 에서 찾을 수 있다.

> Docker Hub에서 제공하는 python image들은 같은 버전임에도 다른 태그를 가진 이미지들이 존재한다. 자세한 설명은 [Docker Docs](https://docs.docker.com/samples/library/python/) 에서 확인 가능하다!
>
> * Alpine : [Alpine Linux](https://www.lesstif.com/pages/viewpage.action?pageId=35356819)
> * Stretch : Debian Linux의 특정 버전
> * Slim : Debian Linux의 특정 버전의 최소용량버전
>
> 정확히는 나도 더 공부하는 걸로!

암튼 우리는 **python:3.7.2-slim** 을 사용할거드앙.

```shell
$ docker images
$ docker run --rm -it python:3.7.2-slim /bin/bash
```

```bash
# pip install django
# django-admin startproject blog
# python blog/manage.py runserver
```

와! 도커를 이용해서 가상컨테이너에 python을 실행하고 그 안에서 django 를 설치한 뒤 프로젝트를 만들고 서버를 올렸다!

그치만 확인할 방법이 없는걸?! 그럼 도커랑 로컬을 연결할 수 있게 포트를 설정해주자.

```shell
$ docker run --rm -it -p 7999:8000 python:3.7.2-slim /bin/bash
```

```bash
# pip install django
# django-admin startproject blog
# python blog/manage.py runserver 0:8000
```

그럼 이제 localhost:7999 로 접속하면 docker의 8000 포트로 연결되어서 Django 프로젝트 화면이 나오게 된다.

### Docker Image 생성

하지만! 도커는 가상머신이니까 내렸다 올렸다 할 때마다 다시 초기화 되서 매번 Django를 설치하고 프로젝트를 만들어야 하잖아?! 짱짱 귀찮으니까 자동화를 해보자.

도커 이미지를 실행할 때 자동으로 해야할 일들을 **Dockerfile**로 정의해주자. 이 도커파일은 우리가 전에 만들었던 Django 프로젝트의 하위에 있다. blog/Dockerfile (manage.py랑 같은 위치)

**Dockerfile** : 대소문자 주의, 확장자 없음 주의! 도커파일은 그냥 도커파일이다!

```
FROM        python:3.7.2-slim
MAINTAINER  caution.dev@gmail.com

RUN         pip install django
WORKDIR     /root

RUN         django-admin startproject blog
WORKDIR     /root/blog
CMD         python manage.py runserver 0:8000
```

한 줄 씩 설명하자면 다음과 같다.

> 이 Docker Image는 python:3.7.2-slim 을 사용한다. python:3.7.2-slim 이미지에 Dockerfile이 있다면 거기에 정의된 명령어들을 수행할거다!
>
> MAINTAINER : 이 Docker Image를 관리하는 관리자의 이메일 주소다!
>
> RUN : 다음 명령어를 수행할거다! 여기서는 django를 설치한다.
>
> WORKDIR : 작업을 수행할 디렉토리를 /root로 변경할 거야! (cd 로 경로 이동하는 거랑 비슷)
>
> RUN : 다음 명령어를 수행할거다! 여기서는 django 프로젝트를 생성한다.
>
> WORKDIR : 작업을 수행할 디렉토리를 /root/blog 로 변경할거야!
>
> CMD : 컨테이너가 시작되었을 때 수행할 명령. Dockerfile에서 한 번만 사용할 수 있다. 여기서는 서버를 띄운다.

Dockerfile을 다 만들었으면 이제 Docker Build 를 수행하여 Docker Image를 만든다.

```shell
$ docker build -t blog .
$ docker images
```

docker images 명령어를 통해 만들어진 image 들을 확인할 수 있다.

자 이제 다시 docker를 실행시켜보자!

```shell
docker run --rm -it -p 7999:8000 blog
```

우왕 그러면 자동으로 django가 켜지고 서버까지 켜지는 걸 확인할 수 있다. localhost:7999 를 열면? 아마 로케트가 나올거다!

### local 파일을 이용하여 docker에 서버 올리기

그런데 위에껄 하면 매번 새로운 프로젝트가 생성되고 그걸 켠다. 그럼 매번 컨테이너 내려가면 데이터 다 날아가는데 껏다 킬때마다 0부터 다시 코딩해야 한다. WHF!!!!!!!!!!!!!!!!

 그러려고 만든 Docker가 아니니까, 우리가 이미 로컬에 만들었던 프로젝트를 이용해서 Docker에 올려보자.

**Dockerfile** 을 수정한다.

```
FROM        python:3.7.2-slim
MAINTAINER  caution.dev@gmail.com

COPY        blog/requirements.txt     /tmp/requirements.txt
RUN         pip install -r /tmp/requirements.txt

COPY        .     /root/app
WORKDIR     /root/app/blog


CMD         python manage.py runserver 0:8000
```

새로운 명령어인 COPY가 생겼다. 이건 그야말로 로컬에 있는 파일을 Docker 로 복사할 때 사용한다.

앞서 만들었던 requirements.txt에는 django 를 포함한 현재 환경에서 사용되는 패키지들이 정의되어 있다. 그래서 이 파일을 복사한 뒤 RUN 명령어를 통해 이 파일에 있는 패키지들을 docker에서 설치한다. (결론적으로는 그러니까 장고를 설치해줄거다!)

그리고 현재 로컬에서 만들었던 Django project 를 그대로 복사한다. 매번 Django 프로젝트를 새로 생성하지 않고 로컬에서 작업한 내용을 가져다가 쓸 것이다. 0부터 코딩을 다시 할 필요가 없다!

Dockerfile을 수정했으면 다시 Docker Build 로 image를 새로 만들고 Docker를 실행시켜 보자.

``` shell
$ docker build -t blog .
$ docker run --rm -it -p 7999:8000 blog
```

이제 localhost:7999 를 열면? 로케트가 아닌 **Hello Django!** 가 나올거다!

우와! 이제 제대로 **로컬**에서 Docker를 써봤다. 하지만 우리는 로컬서버를 돌리는 게 아니니까 (....) 의미가 없……진 않지만 실제 서버처럼 해보자. 한영님은 EC2를 이용하기로 했으니까 해본다.

## EC2 만들기





ec2 인스턴스를 만들자

보안그룹을 http 와 ssh 를 같이 열어주자

https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html



## .pem 파일 관리

.pem 파일은 인증에 사용되는 key 파일입니다. aws에 접속할 때 필요합니다! 이런 인증에 필요한 키 파일이나 인증서 파일은 ~/.ssh 에 옮겨서 관리합시다~! 그리고 키 파일이니까 권한 설정을 바꿔줍시다.

https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair

```perl
chmod 400 /path/my-key-pair.pem
```



dns ipv4 : ec2-13-55-8-215.ap-southeast-2.compute.amazonaws.com

```perl
ssh -i cocozzang.pem ubuntu@ec2-13-55-8-215.ap-southeast-2.compute.amazonaws.com
```

#### DisallowedHost at / 문제

특정 호스트 이름으로 접속했을 때만 정상적으로 보여주도록 제한합니다

```perl
vi blog/settings.py
```

```python
# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True

ALLOWED_HOSTS = ['.amazonaws.com']
```

비어있다는 건 로컬 상에서만 돌릴 때! 그때는 오류가 안됩니당 :-)

도메인을 정확히 적어도 되지만 ``` '.amazonaws.com' ```정도만 적어줘도 괜찮습니다.

> ls -al 명령어를 입력했을 때 요렇게 나와요!
>
> ```perl
> -rwxr-xr-x 1 ubuntu ubuntu  538 Feb  6 08:15 manage.py*
> drwxrwxr-x 3 ubuntu ubuntu 4096 Feb  6 08:19 mysite/
> ```
>
>

EC2에서 Docker를 사용해 서버 띄워보기

- [설치](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

- ```
    sudo apt update
    sudo apt install \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository \
       "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) \
       stable"
    sudo apt update
    sudo apt install docker-ce docker-ce-cli
  ```



### 로컬에 있는 소스코드를 가져와서 EC2에 배포한다.

* ** scp**를 사용해 EC2로 파일 복사하기
  * `scp -i ~/.ssh/<PEM_FileName>.pem -r . ubuntu@<EC2_Public_Domain>:/home/ubuntu/docker/`

-r 재귀적

. 현재위치

ubuntu@<Publid Domain>



sudo docker build -t mashup -f Dockerfile

sudo docker un --rm -it -p 80:80 mashup

ALLOWED_HOSTS = ['.amazonaws.com', 'loclhost'        ]

docker build -t mashup .

docker images

docker tag mashup cocozzang/mashup-docker-example

docker push cocozzang/mashup-docker-example



sudo docker run --rm -it -p 80:8000 cocozzang/mashup-docker-example
