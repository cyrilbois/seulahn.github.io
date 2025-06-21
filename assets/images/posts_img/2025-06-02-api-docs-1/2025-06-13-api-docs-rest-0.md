---
title: "[API Documentation] REST API ① 특성과 구성 요소, 그리고 리소스"
excerpt: "XML의 요소를 이루는 태그와 속성을 알아보고, 선언, 네임스페이스를 포함한 기초 문법까지 살펴본다." 

categories:
  - Technical Writing
tags:
  - [Technical Writing, API, XML]

permalink: /tw/api-docs-rest-0

toc: true
toc_sticky: true

date: 2025-06-05
last_modified_at: 2025-06-05
---

API 문서

---

# REST API란?

**REST(REpresentational State Transfer) API**는 서로 다른 웹 서비스가 통신하는 가장 인기 있는 접근방식이다. REST에서는 클라이언트와 서버가 **HTTP 프로토콜**을 사용해 데이터를 주고받는다.

REST API에 대해 먼저 알아두면 좋을 핵심 개념이 몇 가지 있다.

- REST는 **프로토콜이 아닌** **디자인 패턴**이다. 프로토콜은 엄격한 형식을 따라야 하지만, 디자인 패턴은 일종의 가이드라인으로 볼 수 있다.
    
    > REST 이전에는 SOAP(Simple Object Access Protocol)이라는 XML 기반의 프로토콜이 많이 쓰였다. REST는 더 단순하고 가볍기 때문에 많은 웹 API에서 사용되고 있다.
    
- REST는 **리소스(resource)**를 중심으로 한다. 리소스는 무언가를 대표하는 데이터를 말한다. 예를 들어, 학습 앱에서는 강사, 학생, 강의, 과제 등이 리소스가 될 수 있다.
    
    > REST에서는 **URL**을 사용해 어떤 리소스에 접근할지 지정하고, 무엇을 할지는 **HTTP 메서드**로 표현한다. 더 자세한 내용은 차차 살펴보겠다.
    > 
- REST는 **무상태(stateless)**하다는 특징이 있다. 이는 서버가 정보를 저장하지 않다는 뜻이다. 즉, 매 요청마다 필요한 정보를 모두 포함해서 보내야 한다.

- REST API는 다양한 형태의 데이터를 주고 받을 수 있다. 주로 **JSON** 형식을 사용하지만, 필요에 따라 XML, 이미지, 영상, 기타 파일 등도 사용할 수 있다.
    
    > 클라이언트는 **헤더(header)**를 사용해 어떤 형식의 응답을 원하는지 서버에 알릴 수 있다. 이 또한 나중에 살펴보자.
    > 

---

# REST API 요청과 구조

앞서 봤듯이, REST API는 웹 서비스 간 데이터를 주고받기 위한 방식이다.  
클라이언트가 서버에 무엇을 **요청(request)**하고, 서버가 그에 **응답(response)**을 보내는 구조로 이루어진다. 먼저 요청이 어떻게 구성되는지 아는 것이 중요합니다.

그 구성 요소를 알아보기에 앞서, 실제 요청이 어떤 모습인지 보자. 챗GPT에게 **여행 예약 시스템의 API**을 예시로 두 가지 시나리오를 만들어 달라고 요청해 보았다.

<mark><strong>시나리오 1</strong>: 사용자가 다음 조건으로 항공권을 검색한다.</mark>
- 출발 도시: 서울
- 도착 도시: 도쿄
- 출발일: 2025년 7월 1일
- 결과: 최대 5개 제한  
- API의 베이스 URL은 `https://api.travelmate.com` 이라고 가정한다.

이때 서버로 전송되는 HTTP 요청은 다음과 같다.

```h
GET https://api.travel.com/flights?from=Seoul&to=Tokyo&date=2025-07-01&limit=5
```

이 요청을 분석해 보면, 크게 **HTTP 메서드**와 **요청 URL**로 구성된다. 요청 URL은 베이스 URL, 엔드포인트 경로, 그리고 쿼리 스트링으로 나뉘어 있다.

- `GET`: **HTTP 매서드** 중 하나다. 서버에 데이터를 보여달라고 요청할 때 사용한다.
- `https://api.travel.com`: 서버의 기본 주소다. 이를 **베이스 URL**이라 부른다.
- `/flights`: 어떤 리소스를 요청하는지 나타내는 **엔드포인트 경로**다. 여기서는 '항공편 목록'을 요청했다.
- 뒤따르는 **쿼리 스트링(query string)**은 데이터를 어떤 조건으로 필터링할지 설정한다.  
    이 예시에서는 다음의 네 가지 **쿼리 매개변수(quary parameter)**가 포함됐다.
    - `?from=Seoul`**:** 출발 도시
    - `&to=Tokyo`: 도착 도시
    - `&date=2025-07-01`: 출발 날짜
    - `&limit=5`: 최대 결과 개수  
  
**정리하면**, 이 요청은 `/flights`라는 항공편 리소스를 `GET` 메서드로 요청하면서, 출발지는 `Seoul`, 도착지는 `Tokyo`, 출발일은 `2025-07-01`, 결과는 최대 `5`개로 제한해달라는 의미다.

<br>

다른 예시도 살펴보자.  

<mark><strong>시나리오 2</strong>: 사용자가 다음 조건으로 항공권을 예약한다.</mark>
- 사용자 ID: 42
- 예약 항공편: TK123
- 탑승객 이름: 홍길동
- 여권 번호: M12345678
- 예약 좌석: 12A

```h
POST https://api.travel.com/bookings
Content-Type: application/json

{
	"userId": 42,
	"flightId": "TK123",
	"passenger": {
		"name": "홍길동",
		"passport": "M12345678"
	},
	"seat": "12A"
}
```

HTTP 매서드와 URL로 시작하는 건 비슷하지만, 여기서 눈여겨 볼 부분은 **헤더**와 **본문(혹은 바디)**다.

- **헤더**는 HTTP 요청이나 응답에 포함되는 부가 정보다. 데이터 형식, 인증 정보, 클라이언트 정보 등을 담는다.  

    그 중에서도 `Content-Type`는 클라이언트가 서버에 보내는 데이터읜 형식을 알려주는 헤더다. 이 예시에서는 `application/json`을 사용해, 본문이 JSON 형식임을 알리고 있다.
    
    >`Content-Type` 외에도 자주 쓰이는 헤더로는 `Authorization`, `Accept`, `Bearer`가 있다. 각각 
    > 
- 이어지는 **본문(body)**은 예약에 필요한 정보를 담고 있다. 본문은 메시지라 부르기도 하며, 많은 데이터를 담아야 할 때 사용한다.

    > 본문은 보통 **JSON**이나 **XML** 형식을 사용하지만, 이미지나 동영상 같은 미디어 파일도 담을 수 있다. 본문은 주로 `POST` 또는 `PUT` 요청에서 사용된다.

---

#  리소스
 
REST API의 핵심 개념인 리소스를 조금 더 자세히 알아보자. 

앞서 언급했듯이, 리소스란 서버가 관리하는 데이터 단위를 말한다. REST에서는 이 리소스를 URL 경로 안에 표시한다. 사용자, 게시물, 상품 등 우리가 웹에서 다루는 거의 모든 것이 리소스가 될 수 있다.   

리소스의 두 가지 특징을 살펴보자. 

- 각 리소스는 **고유한 ID**를 가지고 있다. 특정 리소스를 지정하려면 이 ID를 쓰면 된다.
 
    > 예를 들어, 음식 배달 앱의 API를 생각해 보자.  
    > 모든 음식점 목록을 조회하려면 다음과 같이 요청한다.
    >    
    > `GET http://api.example.com/restaurants`
    >  
    > 반면, ID가 42인 음식점 하나만 조회하려면 다음처럼 ID를 명시해야 한다.
    >   
    > `GET http://api.example.com/restaurants/42`

- 한 리소스는 **다른 리소스를 포함**할 수 있다. 이 경우, 포함하는(상위) 리소스를 먼저 경로에 명시한다.

    > 예를 들어, 음식점 42번의 메뉴를 조회하려면 다음과 같이 요청하면 된다.
    >    
    > `GET http://api.example.com/restaurants/42/menus`
    >   
    > 만약 그중 ID가 18인 메뉴만 조회하려면, 다음과 같이 메뉴의 ID도 명시해야 한다.
    >   
    > `GET http://api.example.com/restaurants/42/menus/18`

<br>

다음 글부터는 REST API의 주요 구성요소인 쿼리 매개변수, 헤더, 인증을 본격적으로 다뤄보겠다.

---
 
**참고자료**

- *Udemy - [Learn API Technical Writing 2: REST for Writers](https://www.udemy.com/course/learn-api-technical-writing-2-rest-for-writers/)*
- *GeeksforGeeks - [REST API Introduction](https://www.geeksforgeeks.org/node-js/rest-api-introduction/)*
- *Lonti - [The key ingredients of RESTful APIs: resources, representations, and statelessness](https://www.lonti.com/blog/the-key-ingredients-of-restful-apis-resources-representations-and-statelessness)*