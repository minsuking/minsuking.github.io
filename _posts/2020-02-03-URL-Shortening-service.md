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

> *면접 시작할 때 요구사항을 명확하게 해야한다. 면접관이 염두에 두고있는 시스템의 정확한 범위를 찾기위해 반드시 질문을한다.<br>
