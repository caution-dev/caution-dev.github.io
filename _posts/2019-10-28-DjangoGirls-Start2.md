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

**Query Set은 SQL Query 구문과 그 구문에 대한 데이터를 가지는 객체**입니다.

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

`QuerySet` 을 조금 더 살펴봅시다. QuerySet은 아까 제가 `"SQL Query 구문과 그 구문에 대한 데이터를 가지는 객체입니다."` 라고 적어놨어요. 위의 결과물을 보면 데이터를 가지고 있는 것은 확인됐는데, `SQL Query` 구문은 어디있을까요?

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

하지만 `print(allPosts)`를 입력하면?

```python
In [6]: print(allPosts)
<QuerySet [<Post: Django 스터디 Day 1>, <Post: Django 스터디 Day 2>]>
```

우리에게 데이터를 보여주기 위해서 직접 Database에서 `query`를 수행하여 데이터를 가져옵니다! 매번 데이터를 가져오는 것이 아니라 **꼭 필요한 시점에 Database에 접근하기 때문에** 좀 더 효율적이겠죠?

> 그렇다면 QuerySet은 언제 Database에 접근할까요?
> 이건 [Django 공식홈페이지 (When querysets are evaluated?)](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#when-querysets-are-evaluated)에 잘 나와 있답니다. 한 번 정독해보시는 걸 추천..! 아직 저도 안해봤지만요 헤헤헤
