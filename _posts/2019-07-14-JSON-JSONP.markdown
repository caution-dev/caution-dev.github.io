---
layout: post
title:  "JSON vs JSONP"
date:   2019-07-14 09:38:19 +0900
categories: CS
tags: JSONP JSON
author: Juhee Kim
mathjax: true
---

* content
{:toc}

> 2019.07.01에 작성되었습니다.

요새 하이퍼커넥트(우왕) 에서 인턴쉽을 하고 있습니다!!!!!!!! *영광영광 대영광!!*

1주차 인턴쉽의 목표는 **Flickr API를 사용해서 이미지 피드 만들기**와 RxSwift를 공부하기 입니다.

RxSwift를 공부하되 RxSwift 없이 프로젝트를 개발해보는 것이 목표라서 RxSwift를 공부하기 앞서 Flickr API를 살펴보았습니다.

### Flickr API
사용할 API는 다음곽 같습니다.

```https://www.flickr.com/services/feeds/photos_public.gne```

이를 postman 에서 호출하면 다음과 같은 결과가 나옵니다!
```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>
<feed xmlns="http://www.w3.org/2005/Atom"
      xmlns:dc="http://purl.org/dc/elements/1.1/"
      xmlns:flickr="urn:flickr:user"
      xmlns:media="http://search.yahoo.com/mrss/">
    <title>Uploads from everyone</title>
    <link rel="self" href="http://www.flickr.com/services/feeds/photos_public.gne" />
    <link rel="alternate" type="text/html" href="https://www.flickr.com/photos/"/>
    <id>tag:flickr.com,2005:/photos/public</id>
    <icon>https://combo.staticflickr.com/pw/images/buddyicon.gif</icon>
    <subtitle></subtitle>
    <updated>2019-07-01T06:16:46Z</updated>
    <generator uri="https://www.flickr.com/">Flickr</generator>
    <entry>
        <title>DAXN_18-07-07_Canon EOS 7D_IMG_0899.jpg</title>
        <link rel="alternate" type="text/html" href="https://www.flickr.com/photos/xeiss/48166952616/"/>
        <id>tag:flickr.com,2005:/photo/48166952616</id>
        <published>2019-07-01T06:16:46Z</published>
        <updated>2019-07-01T06:16:46Z</updated>
        <flickr:date_taken>2018-07-07T17:20:39-08:00</flickr:date_taken>
        <dc:date.Taken>2018-07-07T17:20:39-08:00</dc:date.Taken>
        <content type="html">			&lt;p&gt;&lt;a href=&quot;https://www.flickr.com/people/xeiss/&quot;&gt;xeiss&lt;/a&gt; posted a photo:&lt;/p&gt;

&lt;p&gt;&lt;a href=&quot;https://www.flickr.com/photos/xeiss/48166952616/&quot; title=&quot;DAXN_18-07-07_Canon EOS 7D_IMG_0899.jpg&quot;&gt;&lt;img src=&quot;https://live.staticflickr.com/65535/48166952616_489f047e57_m.jpg&quot; width=&quot;240&quot; height=&quot;160&quot; alt=&quot;DAXN_18-07-07_Canon EOS 7D_IMG_0899.jpg&quot; /&gt;&lt;/a&gt;&lt;/p&gt;

</content>
        <author>
            <name>xeiss</name>
            <uri>https://www.flickr.com/people/xeiss/</uri>
            <flickr:nsid>26977653@N00</flickr:nsid>
            <flickr:buddyicon>https://farm6.staticflickr.com/5463/buddyicons/26977653@N00.jpg?1375250332#26977653@N00</flickr:buddyicon>
        </author>
        <link rel="enclosure" type="image/jpeg" href="https://live.staticflickr.com/65535/48166952616_489f047e57_b.jpg" />
        <category term="2018" scheme="https://www.flickr.com/photos/tags/" />
        <category term="7d" scheme="https://www.flickr.com/photos/tags/" />
        <category term="holiday" scheme="https://www.flickr.com/photos/tags/" />
        <category term="mission" scheme="https://www.flickr.com/photos/tags/" />
        <category term="pas" scheme="https://www.flickr.com/photos/tags/" />
        <category term="pasikuda" scheme="https://www.flickr.com/photos/tags/" />
        <category term="srilanka" scheme="https://www.flickr.com/photos/tags/" />
        <category term="trip" scheme="https://www.flickr.com/photos/tags/" />
        <category term="vacation" scheme="https://www.flickr.com/photos/tags/" />
        <displaycategories>
            </displaycategories>
    </entry>
    ...
</feed>
```
**우왕 XML이다**...

XML을 파싱하는 것 말고 JSON으로 받아오는 방법이 있을거라고 생각해서 **API 문서를 탐독하기 시작**합니다.

[Flickr - Feed Format](https://www.flickr.com/services/feeds/)문서에 따르면 rss2, rss1, Atom 등을 비롯해서 php, php_serial, csv, **json**, sql, yaml, cdf등 다양한 format으로 요청할 수 있다고 합니다.

그리고 원하는 format을 요청하려면 **format 매개 변수를 사용**하라고 하네요. 그래서 postman에 **query parameter**를 추가해줬습니다.
```
https://www.flickr.com/services/feeds/photos_public.gne?format=json
```
그 결과는?
```xml
jsonFlickrFeed({
		"title": "Uploads from everyone",
		"link": "https:\/\/www.flickr.com\/photos\/",
		"description": "",
		"modified": "2019-07-01T15:00:13Z",
		"generator": "https:\/\/www.flickr.com",
		"items": [
	   {
			"title": "Semana de la Dulzura",
			"link": "https:\/\/www.flickr.com\/photos\/144395899@N06\/48170431461\/",
			"media": {"m":"https:\/\/live.staticflickr.com\/65535\/48170431461_6d238cef55_m.jpg"},
			"date_taken": "2019-07-01T11:20:12-08:00",
			"description": " <p><a href=\"https:\/\/www.flickr.com\/people\/144395899@N06\/\">claudiazwenguer<\/a> posted a photo:<\/p> <p><a href=\"https:\/\/www.flickr.com\/photos\/144395899@N06\/48170431461\/\" title=\"Semana de la Dulzura\"><img src=\"https:\/\/live.staticflickr.com\/65535\/48170431461_6d238cef55_m.jpg\" width=\"216\" height=\"240\" alt=\"Semana de la Dulzura\" \/><\/a><\/p> ",
			"published": "2019-07-01T15:00:13Z",
			"author": "nobody@flickr.com (\"claudiazwenguer\")",
			"author_id": "144395899@N06",
			"tags": ""
	   }
    ]}
)
```

뭔가 깔끔해진게, JSON인 것 같은데 **jsonFlickrFeed**라는 이름으로 감싸져 있습니다. 우리가 알던 JSON의 형태가 아니네요.

그래서 쟤를 어떻게 하면 벗기고 안의 JSON을 가져올 수 있을까, 한참을 찾아보면서 저 형식이 JSONP라는 것을 알게됐습니다.

## JSONP?
그래서 JSONP가 뭔가요? **도와줘요 위키피디아!**
> JSONP(JSON with Padding 또는 JSON-P[1])는 클라이언트가 아닌, 각기 다른 도메인에 상주하는 서버로부터 데이터를 요청하기 위해 사용된다. 2005년에 Bob Ippolito가 제안하였다.[2] JSONP는 동일-출처 정책을 우회하는 데이터의 공유를 가능하게 한다. 이 정책은 페이지의 출처 밖에서 가져온 미디어 DOM 요소나 XHR 데이터를 읽기 위해 자바스크립트를 실행하는 것을 허용하지 않는다. 사이트의 스킴, 포트 번호, 호스트 이름의 집합은 출처로 식별된다. 상속 비보안 문제로 인해 JSONP는 CORS로 대체되고 있다.

### 예시
좀 더 쉽게 알아보면, JSONP는 동일-출처 정책을 우회하면서 안전하게 데이터를 요청하기 위해 사용되는 방식입니다.
domain이 flickr.com이 아닌 외부에서 Javascript로 다음과 같이 Flickr API를 호출하면 문제가 발생합니다.
```javascript
var xmlhttp = new XMLHttpRequest();
xmlhttp.open('GET', 'https://www.flickr.com/services/feeds/photos_public.gne?format=json', true);
xmlhttp.onload = function () {
  console.log('Retrieved Data: ' + xmlhttp.responseText);
};
xmlhttp.send(); // -> 교차 출처 요청 차단
```

만약 script 태그에 JSON 데이터를 직접적으로 삽입하면 JSON 데이터를 교차 출처 정책에 관계 없이 불러올 수 있습니다.
```javascript
<script type="application/javascript" src="https://www.flickr.com/services/feeds/photos_public.gne?format=json">
</script>
```
하지만 JSON 형태로 데이터가 넘어온다면, src attribute에 `block`이 들어가는 것으로 인식해서 문법 오류가 발생합니다.

그래서 파라미터로 API에 대한 callback 함수를 지정해서 JSON데이터를 감싸주도록 합니다.
#### javascript
```javascript
<script type="application/javascript"
    src="https://www.flickr.com/services/feeds/photos_public.gne?format=json&callback=jsonFlickrFeed">
</script>
```
#### 결과
```
jsonFlickrFeed({...})
```
이렇게 되면 문법오류가 발생하지 않고 API의 콜백함수로 지정된 `jsonFlickrFeed()`에 파라미터로 JSON이 전달될 수 있습니다.

이렇게 CrossDomain 이슈를 해결하기 위한 장치였던 JSONP가! 저에게는 걸림돌이 되었습니다. 아무리 JSONP를 JSON으로 파싱하려 해봐도 Invalid JSON이라는 결과만이 따라왔기 때문이죠.

## JSONP to JSON?
JSONDecoder를 통해서 JSONP를 JSON으로 변환하기는 어려웠습니다. (애초에 JSON이 아니니까요)
그래서 다음과 같은 방법들을 시도해봤습니다.

### split JSONP Data
컨셉은 JSON으로 Decode 하기 전에 Data에서 callback함수 이름인 `jsonFlickrFeed(`와 마지막 `)`를 제거하는 거였습니다.
- APICall 당시 응답 결과가 JSONP인지 JSON인지, JSONP라면 그 callback함수 이름을 param으로 전달한다.
- Response가 왔을 때 JSONP라면 callback 함수 이름 + 1만큼 앞에서 잘라내고, 뒤에서 1만큼 잘라낸다.
- 잘린 Data를 JSON으로 Decode 한다.

아주 불가능한 것은 아니었지만, 코드가 매---우 더러웠습니다. API 호출을 함에 있어서 파라미터로 JSONP여부와 콜백함수 이름을 넘겨야 한다는 것이 **API 호출할 때 이미 그 결과를 알고 있어야 한다.**는 의미와 같았기 때문입니다.

### nojsoncallback
자 더럽한 코드는 잠깐 내려놓고, Flickr API docs를 탐독하기 시작합니다.
그리고 정답은 이미 [Flickr Docs - JSON 응답형식](https://www.flickr.com/services/api/response.json.html)에서 제공하고 있습니다.

> #### 콜백 함수
> 함수 래퍼가 없는 원본 JSON을 원하면 값이 1인 nojsoncallback 매개 변수를 요청에 추가하십시오.
>
> 사용자 고유의 콜백 함수 이름을 정의하려면 원하는 이름의 jsoncallback 매개 변수를 값으로 추가하십시오.

네... 오늘의 교훈... Docs를 **제대로** 읽자..............

```
https://www.flickr.com/services/feeds/photos_public.gne?format=json&nojsoncallback=1
```
그 결과는?
```xml
{
    "title": "Uploads from everyone",
    "link": "https://www.flickr.com/photos/",
    "description": "",
    "modified": "2019-07-01T10:00:00Z",
    "generator": "https://www.flickr.com",
    "items": [
	   {
			"title": "Semana de la Dulzura",
			"link": "https:\/\/www.flickr.com\/photos\/144395899@N06\/48170431461\/",
			"media": {"m":"https:\/\/live.staticflickr.com\/65535\/48170431461_6d238cef55_m.jpg"},
			"date_taken": "2019-07-01T11:20:12-08:00",
			"description": " <p><a href=\"https:\/\/www.flickr.com\/people\/144395899@N06\/\">claudiazwenguer<\/a> posted a photo:<\/p> <p><a href=\"https:\/\/www.flickr.com\/photos\/144395899@N06\/48170431461\/\" title=\"Semana de la Dulzura\"><img src=\"https:\/\/live.staticflickr.com\/65535\/48170431461_6d238cef55_m.jpg\" width=\"216\" height=\"240\" alt=\"Semana de la Dulzura\" \/><\/a><\/p> ",
			"published": "2019-07-01T15:00:13Z",
			"author": "nobody@flickr.com (\"claudiazwenguer\")",
			"author_id": "144395899@N06",
			"tags": ""
	   }
    ]
}
```

네... 혼자 삽질했는데 JSONP JSON으로 바꿔보겠다고 시간날린게 아까워서 써봤습니다. 헤헿
