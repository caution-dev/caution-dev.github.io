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
  * **Create** : 새로운 데이터 추가하기
  * **Order By** : 데이터를 정렬해보기
  * **QuerySet Chaining** : 조건들을 연결하기
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

> 저는 좀 더 향상된 기능을 가진 셸을 사용하기 위해 IPython을 설치해서 사용중입니다  
> `pip install ipython` 명령으로 IPython을 설치하면, manage.py shell 명령을 실행했을 때, 기본 셸 대신 IPython셸로 실행되어 좀 더 편하게 많은 기능들을 사용할 수 있습니다!

#### Post 데이터 가져오기
이 `Django Shell`에서는 현재 위치의 `Django Project`에 정의된 Class들에 접근이 가능합니다! 그럼 이제 어떤 포스팅들이 있는지 바로바로 가져와볼게요 :)

```python
Post.objects.all()
```

오잉, 그럼 바로 오류가 나타나요..! Post 가 정의되지 않았다고 하는데요?

![images](/images/django/django_girls_post_import_error.png)

> 명령어를 입력하는 화면이 저랑 다르더라도 걱정마세요! 원래는 `>>>` 요렇게 시작해야하는 게 맞는데 저는 `IPython`을 설치해서 사용하고 있기 때문에 다른 화면으로 보이는 거랍니다. XD

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
In [3]: all_posts = Post.objects.all()
```

이번에는 아마 포스팅 리스트들이 나타나지 않았을 거에요. 그 상태에서 다음 구문들을 작성해봅시다.

```python
In [4]: all_posts.query     
Out[4]: <django.db.models.sql.query.Query at 0x10dc53550>

In [5]: print(all_posts.query)  
SELECT "blog_post"."id", "blog_post"."author_id", "blog_post"."title", "blog_post"."text", "blog_post"."created_date", "blog_post"."published_date" FROM "blog_post"
```

`QuerySet` class는 `query`라는 `attribute`를 가집니다. 이 `query`가 실제 Database에서 데이터를 불러올 때 사용되는 `SQL Query`입니다. 형태가 조금 다르지만, 위에서 작성했던 SQL문과 유사한 형태를 가지고 있죠? `SELECT` 다음에 어떤 `attribute`들을 출력할 건지 정의하고, `FROM` 구문으로 어떤 테이블에서 데이터를 가져올 건지 명시해주었습니다.

사실 이 `QuerySet`은 생성될 때마다 Database에서 데이터를 가져오지 않습니다. 우리가 `Post.objects.all()`을 입력했을 때 `QuerySet` 인스턴스가 생성되고, 이 때에는 사실 `query`만을 가지고 있고 데이터를 가지고 있지 않습니다.

> 이걸 검증하고 싶은데 어떻게 검증할 수 있을지 지금 딱 방법이 생각나지 않네요.

하지만 `print(all_posts)`를 입력하면?

```python
In [6]: print(all_posts)
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
In [7]: all_posts = Post.objects.all()
```

하지만 내가 **원하는 조건을 가진 `Post`만 가져오려면** 어떻게 해야할까요? 예를 들어, 글쓴이로 검색을 한다고 해보자구요. **내가 작성한 글**만 조회하려면?

```python
In [8]: my_post = Post.objects.get(author=me)
---------------------------------------------------------------------------
NameError Traceback (most recent call last)
<ipython-input-16-eb41f8b5902c> in <module>
----> 1 my_post = Post.objects.get(author=me)

NameError: name 'me' is not defined
```

오 오류가 나타났네요. **me**라는 이름을 가진 객체는 정의되지 않았대요. 이 오류를 잡기 전에 문법을 잠깐 볼까요?

저는 `my_post`라는 변수에 `Post`객체들 중 `author` `attribute`가 `me`에 대응하는 데이터를 가져오도록 명령어를 주었어요. 지난 시간에 `Post`를 정의할 때 `author` 외에 다른 `attribute`들이 있었잖아요? title, text, created_date, published_date 모두 다음과 같은 문법으로 사용할 수 있어요.
```python
Model.objects.get('조건 대상 attribute 명'='조건')
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
In [12]: my_post = Post.objects.get(author=me)
---------------------------------------------------------------------------
MultipleObjectsReturned Traceback (most recent call last)
<ipython-input-20-eb41f8b5902c> in <module>
----> 1 my_post = Post.objects.get(author=me)

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

오오오오오오류다! 오류가 나타났다! 여러분 진정합시다. 오류는 나쁘지 않아요.

오류 문구를 잘 살펴보면 어떤 부분을 수정해야 할 지 알 수 있습니다. 다음 단계에서 왜 이 오류가 나타났는지 살펴볼게요.

### Filter: 여러 기준으로 데이터를 필터링하기

오류의 이름이 뭔가요? `MultipleObjectsReturned` 입니다. 여러 객체가 반환되었다는 거에요. `get` 명령어의 반환값은 **단일 객체**여야 합니다. 제가 작성한 포스팅이 여러 개이기 때문에 이런 오류가 나타난 거에요. 이럴 경우에는 **filter**를 사용해줍니다. 같은 명령을 `get` 대신 `filter`로 바꿔볼게요.

```python
In [13]: my_post = Post.objects.filter(author=me)
In [14]: print(my_post)
<QuerySet [<Post: Django 스터디 Day 1>, <Post: Django 스터디 Day 2>]>
```

오류가 안납니다! **`filter`는 결과값이 없을 수도 있고, 여러 개일 수도 있을 때 사용합니다.** 만약 제목이 'Django'인 포스팅을 필터링 한다면?


```python
In [15]: print(Post.objects.filter(title='Django'))
<QuerySet []>
```

`Django`로 시작하는 포스팅은 2개나 있는데, `title`로 filtering 하니까 결과가 나오지 않아요. 왜냐하면 title 이 완벽히 'Django'인 포스팅은 없거든요. 만약 이 **키워드를 포함한 포스팅들을 검색**하려면 어떻게 해야할까요?

```python
In [16]: print(Post.objects.filter(title__contains='Django'))
<QuerySet [<Post: Django 스터디 Day 1>, <Post: Django 스터디 Day 2>]>
```

짜잔 새로운 문법이 나왔습니다. 문법은 `title`이라는 `attribute`로 조건을 줄 건데, 다음 조건을 포함(contains)하는 데이터들을 가져와라~! 하는 의미에요. `attribute`에 조건을 더하기 위해서는 언더바`_`가 두 개`__`를 붙이기로 약속했습니다. **DjangoGirls**에 따르면,

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

`author` `attribute`는 우리가 만든 `class`가 아니라 `django.conf.settings`에 정의되어 있는, Key 이름이 `AUTH_USER_MODEL`인 뭔지 알 수 없지만 어떤 class 타입을 가집니다. 그때 이 타입이 바로 이번 시간에 나타난

```python
In [11]: from django.contrib.auth.models import User
```

이거였네요! 언젠가 까볼 수 있었으면... 이 말을 왜 꺼냈을까요?

하고 싶었던 이야기는, `Post`라는 모델을 작성할 때 저 `USER`라는 타입을 그대로 가져다 쓰지 않고 `ForeignKey`로 연결해서 사용했다는 점이에요. Database에 대한 이해를 가지고 있으신 분들이라면 아마도 알고 계실 수 있지만, 모르신다면 `Database`의 `Primary Key`와 `Foreign Key` 개념 정도는 확인해주시는 것도, 좋을 것 같아요 :)

#### 발행날짜로 필터링하기
자 우리의 최종 목표가 뭐였는지 기억하시나요? 바로 **발행날짜(`published_date`)가 최신인 포스팅들을 보여주기** 였어요. 그러면 이제 **발행된 포스팅들**을 불러와봅시다.

```python
In [12]: from django.utils import timezone    

# lte 는 less than equal의 약자입니다.          
In [13]: Post.objects.filter(published_date__lte=timezone.now())
Out[13]: <QuerySet []>
```

음? 아무 데이터도 없네요? 관리자 페이지에서 데이터를 다시 살펴보죠.
![images](/images/django/django_empty_published_date.png)

`published_date`가 비어있네요. `Post` model을 만들 때 `published_date` `attribute`를 `blank=True, null=True`로 주었던 거 기억하세요? 그렇기 때문에 `Post` 객체에 `published_date`는 값이 있을 수도, 없을 수도 있답니다.(그걸 우리는 nullable하다고 합니다.)

발행된 포스팅들만 보여주어야 하니까 포스팅에 `publish()` `method`를 호출해봅시다.

`all_posts`에서 첫 번째 `post`만 발행하려면?
```python
In [14]: all_posts[0].publish()
In [15]: Post.objects.filter(published_date__lte=timezone.now())
Out[15]: <QuerySet [<Post: Django 스터디 Day 1>]>

```

짠! 신기하지 않나요? 여러분 `all_post`의 `Type`이 뭐였죠?

```python
In [6]: print(all_posts)
<QuerySet [<Post: Django 스터디 Day 1>, <Post: Django 스터디 Day 2>]>
```

`all_post`는 **QuerySet** 인데, `python`의 `List`처럼 `all_posts[0]`문법으로 데이터에 접근할 수 있습니다. 왤까요??? `QuerySet`은 **내부에서 data를 `List`형태로 가지고 있기 때문에**,  `all_posts[0]` 요청이 들어오면 가지고 있는 data에서 0번째에 있는 데이터를 넘겨줍니다! 짱 편하다~!!

### Create : 새로운 데이터 추가하기
자 이번에는 새로운 포스팅을 코드로 추가해봅시다!

```python
In [16]: newPost = Post.objects.create(author=me, title='caution과 함께하는 Django Study', text='재밌당!')                       
In [17]: newPost.publish()
```

짠짠 매우 쉽죠? 새로운 `Post`를 만들기 위해 필요한 기본 데이터들을 넣어주고, `publish()` 로 바로 발행해주었습니다~


### Order By : 데이터를 정렬해보기
음음, 이제 데이터를 원하는 기준으로 정렬할 건데요, 먼저 **만들어진 날짜를 기준으로 오래된 순으로 정렬**을 해봅시다.

##### 생성날짜로 오름차순 정렬
```python
# order_by('조건이 될 attribute 이름')
In [18]: Post.objects.order_by('created_date')                               
Out[18]: <QuerySet [<Post: Django 스터디 Day 1>, <Post: Django 스터디 Day 2>, <Post: caution과 함께하는 Django Study>>
```

별도 조건을 주지 않으면 날짜가 오래된 순 부터 출력됩니다. 만약 **최신순으로 정렬**하고 싶다면요?
```python
# 내림차순으로 정렬하려면 -를 붙여줍니다.
##### 생성날짜로 내림차순 정렬
In [19]: Post.objects.order_by('-created_date')
Out[19]: <QuerySet [<Post: caution과 함께하는 Django Study>, <Post: Django 스터디 Day 2>, <Post: Django 스터디 Day 1>]>
```

`published_date`로 정렬하면 어떨까요? 아까 `filter`를 사용할 때는 `published_date`가 null이면 보여지지 않았어요.

##### 발행날짜로 오름차순 정렬
```python
In [20]: Post.objects.order_by('published_date')
Out[20]: <QuerySet [<Post: Django 스터디 Day 2>, <Post: caution과 함께하는 Django Study>, <Post: Django 스터디 Day 1>]>
```

오, `published_date`가 없는 경우에는 맨 앞에 나오네요. 그럼 내림차순으로 할 때는 결과가 다를까요?

##### 발행날짜로 내림차순 정렬
```python
In [21]: Post.objects.order_by('-published_date')
Out[21]: <QuerySet [<Post: caution과 함께하는 Django Study>, <Post: Django 스터디 Day 1>, <Post: Django 스터디 Day 2>]>
```

오오... 내림차순일 땐 `published_date`가 없는 경우가 맨 뒤로 가네요. 조심해야겠는걸요?

### QuerySet Chaining : 조건들을 연결하기
우리의 **최종목표**인 **발행날짜(`published_date`)가 최신인 포스팅들을 보여주기** 에 거의 다 온 것 같아요. 하지만, 발행도 되지 않은 포스팅을 보여줄 필요가 있을까요?

그래서 두 개의 조건을 결합하려고 해요.
* **filter** : 발행된 포스팅 (`published_date` 가 null이 아닌 포스팅)
* **oreder by** : 최신 발행 순으로 보여주기 (`published_date`로 내림차순)

이 조건을 코드로 짜보면?
```python
In [22]: Post.objects.filter(published_date__lte=timezone.now()).order_by('-published_date')           
Out[22]: <QuerySet [<Post: caution과 함께하는 Django Study>, <Post: Django 스터디 Day 1>]>
```

와! 드디어 우리가 원하는 데이터를 가져왔어요 :)

원하는 만큼 데이터를 가지고 놀았으니, 이제 그만 `shell`을 종료할 시간입니다.
```python
In [23]: exit()    
```

## Dyanamic Data Set + Template
쨔잔~ 이제 원하는 데이터를 가져왔다면 이걸 화면에서 보여주고 싶어요! 그러려면 데이터(`model`)와 화면(`template`)을 연결하는 작업이 필요합니다~

이 역할을 `Django`에서 `view`가 하고 있습니다. `blog/views.py` 파일을 열어보죠.

`post_list(request)`가 호출되면 `post_list.html`파일을 `response`로 넘겨주는 코드가 작성되어 있을 거에요. 하지만 이제 단순히 `HTML`파일을 넘겨주지 않고, 이 템플릿 파일에 데이터를 연결해서 넘겨줄거에요.

먼저 그러기 위해선 `model`을 불러와야 합니다.

```python
from django.http import HttpResponse
from django.shortcuts import render
# 이 부분이 추가됐어요!
from .models import Post


def post_list(request):
    return render(request, 'post_list.html')
```

> from 다음에 있는 마침표(.)는 현재 디렉토리 또는 애플리케이션을 의미합니다. 동일한 디렉터리 내 views.py, models.py파일이 있기 때문에 . 파일명 (.py확장자를 붙이지 않아도)으로 내용을 가져올 수 있습니다.
>
>(출처: DjangoGirls)

이제 `post_list` `method` 내부에서 우리가 필요한 데이터를 가져와서 `posts`라는 변수에 담아볼게요. 위에서 사용했던 `QuerySet`을 그대로 사용할 거에요!


```python
from django.http import HttpResponse
from django.shortcuts import render
from django.utils import timezone
from .models import Post


def post_list(request):
  posts = Post.objects.filter(published_date__lte=timezone.now()).order_by('-published_date')   
    return render(request, 'post_list.html')
```

그리고 나서, `post_lisht.html`을 기반으로 `response`를 `render`할 때 사용할 수 있도록 데이터 `{posts: posts}`를 넘겨줄게요.

## 발행날짜가 최신인 포스팅들을 보여주기
자! 이제 다시 [post-list 화면](http://127.0.0.1:8000/post-list/)에 들어가 볼까요?

이전과 바뀐게 있다면 시력을 의심해보아야할 겁니다~!

renderer가 response를 만들 때 사용할 수 있도록 data를 넘겨주긴 했지만, 실제 템플릿에서 이 데이터를 어떻게 사용할 것인지에 대해서는 알려주지 않았어요. 그래서 이제부터 해야할 일은 템플릿에서 데이터를 가지고 화면을 그리는 일을 할거에요!

### 포스팅 리스트 출력하기
이제 `templates/post_list.html` 파일을 열어봅시다.

쉬운 것부터 해보자구요. 받아온 데이터를 그대로 출력해보는 건 어때요?

```html
<!doctype html>
<html lang="ko">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport"
              content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
    </head>
    <body>
        <h1>post list</h1>
        {{ posts }}
    </body>
</html>
```

`post_list` 화면을 새로고침해봅시다!

![images](/images/django/django_post_list_0.png)

오우. 예쁘진 않지만 데이터가 잘 나오고 있네요. `HTML` Template 내부에서 `{{ 변수명 }}`을 사용하면 선언된 변수에 접근할 수 있습니다.

 그럼 이제 좀 더 예쁘게 볼 수 있도록 html 코드를 추가해볼게요.

```html
...
  <body>
      <h1>post list</h1>
      {% for post in posts %}
          <div>
              <p>published: {{ post.published_date }}</p>
              <h1><a href="">{{ post.title }}</a></h1>
              <p>{{ post.text|linebreaksbr }}</p>
          </div>
      {% endfor %}
  </body>
...
```

문법을 잠깐 볼게요. `{% %}`이게 갑자기 나타났어요. 요건 위에서 `{{ }}` 사용한 것과 마찬가지로, 이 부분은 html 요소가 아니라 별도 코드로 해석되어야 하는 부분이야~ 하고 알려주는 거에요. 흔히들 이걸 **Template Language**라고 부릅니다.

각 웹프로그래밍 언어별로 사용되는 **Template Language**의 형태가 다를 수 있어요. `Django`에서 사용하는 방식을 알아보려면? [The Django template language](https://docs.djangoproject.com/en/2.2/ref/templates/language/)를 참고하세요!

자 이제 화면을 새로고침해봅시다! 어떤가요?


![images](/images/django/django_post_list_1.png)

엄청엄청 예쁜 것 까진 아니지만, 이렇게 나오면 성공입니다! XD

오늘 포스팅은 여기까지에요 헤헤헤헤헤

다음 시간에 뵈어용! 긴 포스팅 읽어주셔서 감사합니다. XD
