---
layout: post
title:  "Django Study Day 2"
date:   2019-10-28 21:26:06 +0900
categories: Django
tags: Backend Django Python
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

안녕하세요! caution입니다.

지난 [포스팅](https://caution-dev.github.io/django/2019/10/15/DjangoGirls-Start.html)에서는 Django 프로젝트를 위한 설정 및 DjangoGirls 프로젝트를 시작해보았습니다. 블로그 앱을 만들기 위해서 `Post` model 클래스도 만들었고, admin 페이지에 포스팅을 관리할 수 있도록 등록하는 과정까지 진행해보았습니다!

그리고 오늘의 목표는 다음과 같습니다 :)

### 오늘의 목표
* 데이터 가지고 놀기 **(QuerySets)**
  * **Get** : 원하는 데이터 가져오기
  * **Filter** : 여러 기준으로 데이터를 필터링하기
  * **Order By** : 데이터를 정렬해보기
  * 위 조건들을 결합해서 내가 원하는 데이터를 가져오기
* Dynamic Data Set + Template
* **최종목표** : `post_list` 페이지에 **발행날짜(publish)**가 **최신**인 포스팅들을 보여주기

목표가 정해졌으니, 차근차근 달려볼까요~

> 참고로, 이 포스팅 [DjangoGirls]() 튜토리얼 컨텐츠를 기반으로 작성되었습니다.
> 해당 포스팅은 다음 컨텐츠들을 포함합니다. [장고 ORM과 쿼리셋(QuerySets](https://tutorial.djangogirls.org/ko/django_orm/) / [템플릿 동적 데이터](https://tutorial.djangogirls.org/ko/dynamic_data_in_templates/) / [장고 템플릿](https://tutorial.djangogirls.org/ko/django_templates/)


## 데이터 가지고 놀기
지난 시간에 만들었던 [관리자 페이지](http://127.0.0.1:8000/admin/)에서 내가 만들었던 Post 목록을 볼 수 있었습니다. 기억나시죠? 여기에 보이는 데이터들은 **모든** 포스팅들을 보여주고 있어요.

![image](/images/django/django_girls_admin_posts.png)

물론 모든 포스팅을 보는 목록형 페이지도 필요하겠지만, 블로그 앱을 만들려면 더 많은 기능들이 필요할 수 있어요. 예를 들어,

* 포스팅 상세보기
* 포스팅 필터링
* 포스팅 날짜순 조회
* 포스팅 검색

이런 기능은 당연히 있어야겠죠? 이런 기능들이 없다면 원하는 포스팅을 찾으려고 할 때 너무 어려울 것 같아요 ㅠ.ㅠ 하지만 또 이걸 일일이 구현하기에는.. 어려울 수도 있다고 생각해요..! 매번 반복문을 돌면서 조건을 걸어서 필터링 할 수도 없고!

그래서 `Django`에서는 자체적으로 Model 객체들을 여러 조건을 주어 검색할 수 있도록 도와주는 **QuerySet** 클래스가 있습니다.

### QuerySet
이전 시간에 `DB Browser for SQLite`를 통해서 Database에 접근했던 걸 기억하시나요? Database의 데이터를 조회하고, 수정하고, 새로운 데이터를 추가할 수 있는데요, 웹 서버에서는 Database GUI Tool을 사용할 수 없기 때문에 직접 프로그래밍 코드로 데이터를 관리할 수 있도록 `SQL(Structured Query Language)`을 사용합니다.

예를 들어, 모든 포스팅 데이터를 가져오려면?
```sql
select * from Post
```
> 간단히 설명하자면, `Post` 테이블에서 모든 데이터를 출력하는데, 모든 attributes(*)를 출력해 달라는 의미에요.

당황하지 마세요. 저희는 지금 SQL을 배우려는 게 아닙니다. 물론 알고 있다면 더 이해하기 쉽겠지만(또 언젠가는 알아야하지만), `Django`에서는 개발자가 SQL구문을 직접 사용하지 않아도 되도록 `QuerySet`을 만들었어요.

그래서 `QuerySet`이 뭐냐?

**Query Set은 SQL Query 구문과 그 구문에 대한 데이터를 가지는 class**입니다.

아까 모든 포스팅 데이터를 가져오는 SQL query를 작성해보았는데요, 이걸 `Query Set` 형태로 변경해봅시다.

### Django Interactive Console
`Query Set` 객체를 직접 만들어보기 전에, `Django Shell`에 들어가볼 거에요.

이전에 `python` 가상환경 안에 들어갔던 것을 기억하나요? 마찬가지로 `Django Shell`에서는 `python` 명령어를 포함해서 `Django`가 지원하는 여러가지 마법같은 명령어들을 사용할 수 있답니다.

```sh
$ python manage.py shell
```

그럼 다음과 안내문구가 나타나면서 Django Shell 안쪽으로 들어갈 수 있습니다 :)
![images](/images/django/django_girls_django_shell.png)

#### Post 데이터 가져오기
이 `Django Shell`에서는 현재 위치의 `Django Project`에 정의된 Class들에 접근이 가능합니다! 그럼 이제 어떤 포스팅들이 있는지 바로바로 가져와볼게요 :)

```python
Post.objects.all()
```

오잉, 그럼 바로 오류가 나타나요..! Post 가 정의되지 않았다고 하는데요?

![images](/images/django/django_girls_post_import_error.png)

> 명령어를 입력하는 화면이 저랑 다르더라도 걱정마세요! 원래는 `>>>` 요렇게 시작해야하는 게 맞는데 저는 `ipython`을 설치해서 사용하고 있기 때문에 다른 화면으로 보이는 거랍니다. XD

이전 시간에 파일에서 다른 파일에 정의된 `class`나 `method`를 가져와야 할 때 어떤 명령어를 맨 위에 써주었습니다! 바로 `import`인데요, 마찬가지로 `Django Shell`에서도 `blog.models`에 정의되어 있는 `Post` `class`를 `import`해줄게요.

```python
from blog.models import Post
```

자 그리고 나서 다시 위의 구문을 입력해 봅시다.
```python
In [2]: Post.objects.all()     
Out[2]: <QuerySet [<Post: Django 스터디 Day 1>, <Post: Django 스터디 Day 2>]>

```

오, 우리가 작성했던 포스팅이 보이는군요! 포스팅들이 배열`[]`형태로 들어가 있고, 이 포스팅들을 `QuerySet` 이라는 클래스 객체가 가지고 있어요.

`QuerySet` 을 조금 더 살펴봅시다. QuerySet은 아까 제가 `"SQL Query 구문과 그 구문에 대한 데이터를 가지는 class입니다."` 라고 적어놨어요. 위의 결과물을 보면 데이터를 가지고 있는 것은 확인됐는데, `SQL Query` 구문은 어디있을까요?

새로운 `QuerySet` 객체를 만들어봅시다.
```python
In [3]: allPosts = Post.objects.all()
```

이번에는 아마 포스팅 리스트들이 나타나지 않았을 거에요. 그 상태에서 다음 구문들을 작성해봅시다.

```python
In [4]: allPosts.query     
Out[4]: <django.db.models.sql.query.Query at 0x10dc53550>

In [5]: print(allPosts.query)  
SELECT "blog_post"."id", "blog_post"."author_id", "blog_post"."title", "blog_post"."text", "blog_post"."created_date", "blog_post"."published_date" FROM "blog_post"
```

`QuerySet` class는 `query`라는 property를 가집니다. 이 `query`가 실제 Database에서 데이터를 불러올 때 사용되는 `SQL Query`입니다. 형태가 조금 다르지만, 위에서 작성했던 SQL문과 유사한 형태를 가지고 있죠? `SELECT` 다음에 어떤 `attribute`들을 출력할 건지 정의하고, `FROM` 구문으로 어떤 테이블에서 데이터를 가져올 건지 명시해주었습니다.

사실 이 `QuerySet`은 생성될 때마다 Database에서 데이터를 가져오지 않습니다. 우리가 `Post.objects.all()`을 입력했을 때 `QuerySet` 인스턴스가 생성되고, 이 때에는 사실 `query`만을 가지고 있고 데이터를 가지고 있지 않습니다.

> 이걸 검증하고 싶은데 어떻게 검증할 수 있을지 지금 딱 방법이 생각나지 않네요.

하지만 `print(allPosts)`를 입력하면?

```python
In [6]: print(allPosts)
<QuerySet [<Post: Django 스터디 Day 1>, <Post: Django 스터디 Day 2>]>
```

우리에게 데이터를 보여주기 위해서 직접 Database에서 `query`를 수행하여 데이터를 가져옵니다! 매번 데이터를 가져오는 것이 아니라 **꼭 필요한 시점에 Database에 접근하기 때문에** 좀 더 효율적이겠죠?

> 그렇다면 QuerySet은 언제 Database에 접근할까요?
> 이건 [Django 공식홈페이지 (When querysets are evaluated?)](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#when-querysets-are-evaluated)에 잘 나와 있답니다. 한 번 정독해보시는 걸 추천..! 아직 저도 안해봤지만요 헤헤헤 다음에 정리해볼게요!

대략 정리하자면, `QuerySet` 객체는 내부에 데이터를 가져올 수 있는 `query`문을 가지고 있고, 이 `query`문을 사용하여 데이터베이스의 데이터를 가져옵니다. 한 번 데이터를 가져오고 나면 (데이터가 변경되는 등 새로고침되어야 하는 조건이 발생하지 않았다고 하면) 다시 요청이 들어왔을 때 매번 데이터를 가져오는 것이 아니라 이미 가져온 데이터(a.k.a cached data)를 넘겨줍니다.

`QuerySet`에 대해 간략히 알아봤습니다. 이제 이 `class`를 가지고 데이터를 가지고 놀아 봅시다.

### GET: 원하는 데이터 가져오기
사실 이 기능은 위에서 살짝 해보았습니다. 다시 확인해볼까요?

```python
# 모든 Post 객체들을 가져오기
In [7]: allPosts = Post.objects.all()
```

하지만 내가 **원하는 조건을 가진 `Post`만 가져오려면** 어떻게 해야할까요? 예를 들어, 글쓴이로 검색을 한다고 해보자구요. **내가 작성한 글**만 조회하려면?

```python
In [8]: myPosts = Post.objects.get(author=me)
---------------------------------------------------------------------------
NameError Traceback (most recent call last)
<ipython-input-16-eb41f8b5902c> in <module>
----> 1 myPosts = Post.objects.get(author=me)

NameError: name 'me' is not defined
```

오 오류가 나타났네요. **me**라는 이름을 가진 객체는 정의되지 않았대요. 이 오류를 잡기 전에 문법을 잠깐 볼까요?

저는 `myPosts`라는 변수에 `Post`객체들 중 `author` property가 `me`에 대응하는 데이터를 가져오도록 명령어를 주었어요. 지난 시간에 `Post`를 정의할 때 `author` 외에 다른 property들이 있었잖아요? title, text, created_date, published_date 모두 다음과 같은 문법으로 사용할 수 있어요.
```python
Model.objects.get('조건 대상 property 명'='조건')
```

자 다시 돌아와서, `author=me`라는 조건을 주려면 현재 사용자 객체(me)를 정의해야 해요. 그러려면 아까 `Post` `class` 를 사용하기 전처럼 `User` `class`를 `import`해줍니다.

```python
In [9]: from django.contrib.auth.models import User
```

지난 번에 우리가 `manage.py`의 명령어로 관리자 사용자를 만들었던 걸 기억하시나요? 사용자는 여러명이 있을 수도 있으니, 현존하는 모든 사용자들을 확인해봅시다.

```python
In [10]: User.objects.all()                     
Out[10]: <QuerySet [<User: caution>]>
```

오, 일단 사용자는 하나뿐이군요. 그럼 이 사용자를 `me`에 넣어주면, `author=me`를 사용할 수 있겠어요.


```python
In [11]: me = User.objects.get(username='caution')  
In [12]: myPosts = Post.objects.get(author=me)
---------------------------------------------------------------------------
MultipleObjectsReturned Traceback (most recent call last)
<ipython-input-20-eb41f8b5902c> in <module>
----> 1 myPosts = Post.objects.get(author=me)

~/.pyenv/versions/3.6.8/envs/djangogirls-env/lib/python3.6/site-packages/django/db/models/manager.py in manager_method(self, *args, **kwargs)
     80         def create_method(name, method):
     81             def manager_method(self, *args, **kwargs):
---> 82                 return getattr(self.get_queryset(), name)(*args, **kwargs)
     83             manager_method.__name__ = method.__name__
     84             manager_method.__doc__ = method.__doc__

~/.pyenv/versions/3.6.8/envs/djangogirls-env/lib/python3.6/site-packages/django/db/models/query.py in get(self, *args, **kwargs)
    410         raise self.model.MultipleObjectsReturned(
    411             "get() returned more than one %s -- it returned %s!" %
--> 412             (self.model._meta.object_name, num)
    413         )
    414

MultipleObjectsReturned: get() returned more than one Post -- it returned 2!

```

오오오오오오류다! 오류가 나타났다! 여러분 진정합시다. 오류는 나쁘지 않아요. 오히려 우리를 도외주는 길잡이이죠. 오류의 이름이 뭔가요? `MultipleObjectsReturned` 입니다. 여러 객체가 반환되었다는 거에요. `Get` 명령어의 반환값은 **단일 객체**여야 합니다. 제가 작성한 포스팅이 여러 개이기 때문에 이런 오류가 나타난 거에요. 이럴 경우에는 **filter**를 사용해줍니다. 같은 명령을 get 대신 filter로 바꿔볼게요.

```python
In [13]: myPosts = Post.objects.filter(author=me)
In [14]: print(myPosts)
<QuerySet [<Post: Django 스터디 Day 1>, <Post: Django 스터디 Day 2>]>
```

오류가 안납니다! **`filter`는 결과값이 없을 수도 있고, 여러 개일 수도 있을 때 사용합니다.** 만약 제목이 'Django'인 포스팅을 필터링 한다면?


```python
In [15]: print(Post.objects.filter(title='Django'))
<QuerySet []>
```

`Django`로 시작하는 포스팅은 2개나 있는데, title로 filtering 하니까 결과가 나오지 않아요. 만약 `Django`가 포함된 포스팅들을 검색하려면 어떻게 해야할까요?

```python
In [16]: print(Post.objects.filter(title__contains='Django'))
<QuerySet [<Post: Django 스터디 Day 1>, <Post: Django 스터디 Day 2>]>
```

짜잔 새로운 문법이 나왔습니다. 문법은 title이라는 property로 조건을 줄 건데, 다음 조건을 포함(contains)하는 데이터들을 가져와라~! 하는 의미에요. property에 조건을 더하기 위해서는 언더바`_`가 두 개`__`를 붙이기로 약속했습니다. **DjangoGirls**에 따르면,

> title와 contains 사이에 있는 밑줄(_)이 2개(__)입니다. 장고 ORM은 필드 이름("title")과 연산자과 필터("contains")를 밑줄 2개를 사용해 구분합니다. 밑줄 1개만 입력한다면, FieldError: Cannot resolve keyword title_contains라는 오류가 뜰 거예요.

라고 알려주네요! 신기하죠? 아주 다양한 조건들을 줄 수 있어요. `QuerySet` 조건이 궁금하다면 [QuerySet API Reference](https://docs.djangoproject.com/en/2.2/ref/models/querysets/)를 참고해봅시다 :) 양은 짱짱 길어요.

#### 잠깐 돌아가는 이야기
잠깐만요. 우리가 너무 자연스럽게 `User`라는 Model을 사용하고 있지 않나요? 혹시 지난 번 `Post` model을 작성할 때 `author`에 어떤 데이터가 들어갔는지, 기억하나요? 기억이 나지 않는다면, 프로젝트에서 `blog/models.py`를 열어봅시다.

```python
from django.conf import settings
...

class Post(models.Model):
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    ...
```

`author` property는 우리가 만든 class가 아니라 `django.conf.settings`에 정의되어 있는, Key 이름이 `AUTH_USER_MODEL`인 뭔지 알 수 없지만 어떤 class 타입을 가집니다. 그때 이 타입이 바로

```python
In [11]: from django.contrib.auth.models import User
```

이거였네요! 언젠가 까볼 수 있었으면... 이 말을 왜 꺼냈을까요?

하고 싶었던 이야기는, `Post`라는 모델을 작성할 때 저 `USER`라는 타입을 그대로 가져다 쓰지 않고 `ForeignKey`로 연결해서 사용했다는 점이에요. Database에 대한 이해를 가지고 있으신 분들이라면 아마도 알고 계실 수 있지만, 모르신다면 `Database`의 `Primary Key`와 `Foreign Key` 개념 정도는 확인해주시는 것도, 좋을 것 같아요 :)
