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

<pre><code>
createURL(apiDevKey, originalUrl, customAlias=None, userName = None, expireDate=None)
</code><pre>
**Parameters:**<br>
apiDevKey(String) : 등록된 계정의 개발자 API키, 이것은 할당된 할당량에 기초하여 User를 Control 하는것에 사용될 것.<br>
originalUrl : 원본 url<br>
customAlias=None : url에 대한 사용자의 지정키<br>
userName = None : 인코딩에 사용될 선택적 사용자 이름<br>
expireDate=None : 단축된 url의 만료 날짜<br>

**Returns: String)**<br>
성공적으로 Insert를 했다면 단축 URL을 리턴한다. 그렇지 않으면 error code를 반환한다. <br>

<pre><code>
deleteURL(apiDevKey, urlKey)
</code><pre>
여기서 urlKey는 검색할 단축 url을 나타내는 문자열이다. 삭제에 성공하면 url 제거에 대한 성공메시지가 반환된다.<br>
**우리는 어떻게 abuse를 막고 예방할 수 있을까?** 악의적인 user는 현재설계되어 있는 모든 url키를 소비함으로써 이 서비스를 망하게 할 수 있다. 남용 방지를 위해 apiDevKey를 통해 사용자를 제한 할 수 있다. 각 apiDevKey는 일정기간당 특정 url생성 및 리디렉션 수로 제한 될 수 있다.<br>
