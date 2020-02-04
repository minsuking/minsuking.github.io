---
title: URL Shortening service
tags: [System Design]
style:
color: green
description: This service will provide short aliases redirecting to long URLs.
---
Tiny와 같은 URL 단축 서비스를 설계하기. 이 서비스는 긴 URL로 리다이렉션되는 짧은 aliaces를 제공한다.

### URL Shortening이 필요한 이유.

URL Shortening은 긴 URL에 대한 짧은 aliaces를 만드는데 사용된다. 우리는 이 단축된 aliases를 "short link"라고 부른다. 사용자는 이러한 Short link를 누르면 원래의 URL로 리다리렉션이 된다. Short link는 보여줄때, 출력할때 메시지를 보낼때 혹은 트위터를 할때 많은 공간을 절약해준다. 게다가 사용자들은 짧은 URL을 잘못 입력하는 경우가 있다.<br>

예를들어, 우리는 아래의 페이지를 TinyURL을 통해 줄일경우 :<br>

> https://minsuking.github.io/blog/Key-Characteristics-of-Distributed-Systems<br>

우리는 이런 결과를 얻을것이다.<br>

> http://tiny.cc/scdijz<br>

위를 보게되면 Shortened URL의 경우 실제 URL의 크기에 1/3 정도다.<br>

URL Shortening은 기기간의 링크를 최적화시키고, Audience와 Campaign performance를 분석하기위한 개별 링크를 추적하고, 연결되어있는 원래의 URL을 숨기기위해 사용된다.<br>

### 시스템의 요구사항 및 목표

> * 면접 시작할 때 요구사항을 명확하게 해야한다. 면접관이 염두에 두고있는 시스템의 정확한 범위를 찾기위해 반드시 질문을한다.<br>

URL Shortening system은 다음 요구 사항을 충족 시켜야 한다.<br>

**기능 요구 사항**<br>

1. URL이 주어지면, 이 서비스는 그것을 짦고 Unique한 alias를 만들어야한다.  이것을 위에서 이야기했던 Short link라고 한다. 이 링크는 쉽게 복사하고 붙여넣을 수 있을 정도로 짧아야 한다.<br>

2. User들이 Short link에 접근 할때, 이 서비스는 원래의 링크로 리다이렉트를 해줘야 한다.<br>

3. User는 선택적으로 자신의 url에 대한 사용자 정의 Short link를 선택 할 수 있어야한다.<br>

4. 링크는 표준 기본 시간대가 지나면 만료된다. User는 만료 시간을 지정 할 수 있어야 한다.<br>

**비 기능 요구 사항**<br>
1. 시스템의 가용성이 높아야 한다. 이 시스템이 중단된다면 모든 url 서비스의 url 리다이렉션이 실패하기 때문이다.<br>

2. url 리디렉션은 최소의 지연시간으로 실시간으로 이뤄져야한다.<br>

3. Shortened link는 추측될 수 없어야 한다.<br>

**확장 요구 사항**<br>

1. 분석이 가능해야한다. 예를 들어 리디렉션 횟수<br>

2. 이 서비스는 REST API를 통해 접근 가능해야 한다.<br>

### 시스템 API
> 일단 요구사항을 확정하고 나서 API를 정의하는 것은 항상 옳은 생각이다. 이는 시스템에서 예상되는 사항을 정확하게 명시해야한다.<br>

우리는 SOAP 또는 REST API를 통해 서비스의 기능을 노출시킬 수 있다. 다음은 URL 생성 및 삭제를 위한 API 정의이다.<br>

~~~Java
createURL(apiDevKey, originalUrl, customAlias=None, userName = None, expireDate=None)
~~~
**Parameters:**<br>
apiDevKey(String) : 등록된 계정의 개발자 API키, 이것은 할당된 할당량에 기초하여 User를 Control 하는것에 사용될 것.<br>
originalUrl : 원본 url<br>
customAlias=None : url에 대한 사용자의 지정키<br>
userName = None : 인코딩에 사용될 선택적 사용자 이름<br>
expireDate=None : 단축된 url의 만료 날짜<br>

**Returns: String)**<br>
성공적으로 Insert를 했다면 단축 URL을 리턴한다. 그렇지 않으면 error code를 반환한다. <br>

~~~Java
deleteURL(apiDevKey, urlKey)
~~~
여기서 urlKey는 검색할 단축 url을 나타내는 문자열이다. 삭제에 성공하면 url 제거에 대한 성공메시지가 반환된다.<br><br>
**우리는 어떻게 abuse를 막고 예방할 수 있을까?** 악의적인 user는 현재설계되어 있는 모든 url키를 소비함으로써 이 서비스를 망하게 할 수 있다. 남용 방지를 위해 apiDevKey를 통해 사용자를 제한 할 수 있다. 각 apiDevKey는 일정기간당 특정 url생성 및 리디렉션 수로 제한 될 수 있다.<br>

### 데이터베이스 디자인
> 인터뷰 초기단계에서 DB 스키마를 정의하는것은 다양한 요소들 간의 데이터 흐름을 이해하는 데 도움이 될 것이다.<br>

저장할 데이터의 특성에 대한 몇 가지 관측<br>
1. 우리는 수십억 개의 기록들을 보관해야 한다.
2. 각각의 Object는 용량이 작다.(1K 미만)
3. 어떤 사용자가 url을 만들었는지 저장하는것을 제외한 레코드 사이에는 아무런 relationships가 없다.
4. 이 서비스는 읽을때 무겁다.(read-heavy)

**데이터 베이스 스키마:**<br>
우리는 두 개의 테이블이 필요할 것이다. 하나는 URL 매핑에 관한 정보를 저장하기 위한 테이블이고, 다른 하나는 짧은 링크를 만든 사용자의 데이터를 저장하기 위한 테이블이다.<br>
![Schema](https://user-images.githubusercontent.com/60283244/73647590-2a43b680-46bf-11ea-941d-f9c4c3eea437.JPG)

**어떤 종류의 데이터베이스를 사용해야할까?** 우리는 수십억 개의 row를 저장할 것으로 예상되며, Object간의 relationshipis를 사용할 필요가 없기 때문에 DynamoDB, Cassandra 또는 Riak같은 NoSQL 저장소가 좋은 선택이다. NoSQL의 경우 확장하기가 더 편하다.<br>

### 기본적인 시스템 디자인 그리고 알고리즘
우리가 여기서 해결하고자 하는 문제는 받은 url에 대한 짧고 고유한 키를 생성하는 것이다.<br><br>
TinyURL을 예제를 보자면 단축 URL은 http://tiny.cc/scdijz 이다.그리고 이 URL의 마지막 6자리의 문자는 우리가 생성하고자하는 짧은 키다.<br>

*a. 실제 URL 인코딩 #*<br>
주어진 URL의 고유한 해시(예: MD5 또는 SHA256 등)를 계산할 수 있다. 그런 다음 표시를 위해 해시를 인코딩할 수 있다. 이 인코딩은 base36([a-z ,0-9]) 또는 base62([A-Z, a-z, 0-9])일 수 있으며, '+'와 '/'를 추가하면 Base64 인코딩을 사용할 수 있다. 합리적인 질문은, 짧은 키의 길이가 얼마야 되어야 하는가? 6, 8, 10자?<br>

base64 인코딩을 사용하면 6자 길이의 키를 사용할 경우 64^6 = 687억 개의 문자열이 발생할 수 있다.<br>
base64 인코딩을 사용하면 8자 길이의 키를 사용하면 64^8 = 281조 개의 문자열이 발생할 수 있다.<br>

68.7B의 고유한 문자열을 사용하면 6개의 문자 키가 우리 시스템에 충분하다고 가정해 봅시다.<br>

우리가 MD5 알고리즘을 해시함수로 사용하면 128비트 해시 값이 나올 겁니다. base64 인코딩 후에는 21자를 초과하는 문자열을 얻을 수 있다(각 base64 문자는 해시 값의 6비트를 인코딩하므로). 이제 우리는 짧은 키당 8자밖에 쓸 수 없는 공간이 있는데, 그렇다면 우리는 어떻게 우리의 키를 선택할 것인가? 열쇠는 처음 6자(또는 8자)를 가져갈 수 있다. 이 경우 키 중복이 발생할 수 있으며, 이를 해결하기 위해 인코딩 문자열에서 다른 문자를 선택하거나 문자를 교환할 수 있다.<br>

**우리의 솔루션 문제와 다른점은 무엇인가?** 인코딩 방식에는 다음과 같은 문제가 있다.<br><br>

1. 여러 사용자가 동일한 URL을 입력하면 동일한 단축 URL을 얻을 수 있는데, 이는 허용되지 않는다.
2. URL의 일부가 URL 인코딩된 경우(예: http://www.educative.io/distributed.php?id=design) 그리고 http://www.educative.io/distributed.php%3Fid%3D 설계는 URL 인코딩을 제외하고 동일하다.<br>
**문제 해결 방법:** 입력 URL마다 시퀀스 번호가 증가하여 고유하게 만든 다음 해시를 생성할 수 있다. 하지만 우리는 이 시퀀스 번호를 데이터베이스에 저장할 필요가 없다. 이 접근방식에서 발생할 수 있는 문제는 계속 증가하는 시퀀스 번호일 수 있다. 넘칠 수 있을까? 시퀀스 번호의 증가는 또한 서비스의 성능에 영향을 미칠 것이다.<br><br>

또 다른 해결책은 입력 URL에 사용자 ID(Unique해야 함)를 추가하는 것이다. 그러나 사용자가 로그인하지 않은 경우 고유 키를 선택하도록 사용자에게 요청해야 할 것이다. 이후에도 이슈가 생기면 Unique 열쇠를 얻을 때까지 키를 계속 만들어 내야 한다.
