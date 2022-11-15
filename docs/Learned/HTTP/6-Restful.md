---
layout: post
title: "· REST API Tutorial"
nav_order: 6
parent : HTTP
grand_parent: 📚Learned
permalink: docs/Learned/HTTP/Restful
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>

멋쟁이 사자처럼 백엔드 스터디 그룹원이 함께 [restfulapi.net](https://restfulapi.net/) 글을 번역한 번역본입니다.


<br>


# **1️⃣ Learn REST**



## **What is REST?**

**REST**는 **REpresentational State Transfer**의 약자이며 분산 하이퍼미디어 시스템을 위한 아키텍처 스타일입니다. Roy Fielding은 2000년 그의 유명한 논문에서 이를 처음 제시했습니다.

다른 아키텍처 스타일과 마찬가지로 **REST에는 기본 원칙과 제약 조건이 있습니다.** 서비스 인터페이스를 RESTful로 참조해야 하는 경우 이러한 원칙이 충족되어야 합니다.

> REST 아키텍처 스타일을 따르는 Web API(또는 Web Service)가 REST API입니다.



### **1. REST의 기본 원칙**

[RESTful 아키텍처](https://restfulapi.net/rest-architectural-constraints/)의 6가지 기본 원칙 또는 제약 조건은 다음과 같습니다.



#### 1.1. Uniform Interface

구성 요소 인터페이스에 **[일반성의 원칙](https://www.d.umn.edu/~gshute/softeng/principles.html)**을 적용하여 전체 시스템 아키텍처를 단순화하고 상호 작용의 가시성을 향상시킬 수 있습니다.

여러 아키텍처 제약 조건은 균일한 인터페이스를 확보하고 구성 요소의 동작을 안내하는 데 도움이 됩니다.

다음 **네 가지 제약 조건이 규격화된/균일한 (uniform) REST 인터페이스를 달성**할 수 있게 합니다.

- **리소스 식별**: 인터페이스는 클라이언트와 서버 간의 상호 작용에 관련된 각 리소스를 고유하게 식별해야 합니다.

- **representations 을 통한 리소스 조작**: 리소스는 서버 응답에서 균일한 representations 을 가져야 합니다. API 소비자는 이러한 표현을 사용하여 서버의 리소스 상태를 수정해야 합니다.

  > ( ※ “representation”은 무엇인가? 어떤 리소스의 특정 시점의 상태를 반영하고 있는 정보 )

- **자체 설명 메시지**: 각 리소스 표현에는 메시지 처리 방법을 설명하기에 충분한 정보가 포함되어야 합니다. 또한 클라이언트가 리소스에 대해 수행할 수 있는 추가 작업에 대한 정보도 제공해야 합니다.

- **애플리케이션 상태의 엔진으로서의 하이퍼미디어**: 클라이언트는 애플리케이션의 초기 URI만 가져야 합니다. 클라이언트 응용 프로그램은 하이퍼링크를 사용하여 다른 모든 리소스와 상호 작용을 동적으로 구동해야 합니다.



#### 1.2. Client-Server

클라이언트-서버 디자인 패턴은 관심사를 분리하여 클라이언트와 서버 구성 요소가 독립적으로 발전하는 데 도움이 됩니다.

사용자 인터페이스 문제(클라이언트)와 데이터 스토리지 문제(서버)를 분리하여 여러 플랫폼에서 사용자 인터페이스의 이식성을 개선하고 서버 구성 요소를 단순화하여 확장성을 개선합니다.

클라이언트와 서버가 진화하는 동안 클라이언트와 서버 간의 인터페이스/계약이 깨지지 않도록 해야 합니다.



#### 1.3. Stateless

[stateless](https://restfulapi.net/statelessness/) 상태에서는 클라이언트에서 서버로의 각 요청에 요청을 이해하고 완료하는 데 필요한 모든 정보가 포함되어야 합니다.

서버는 **이전에 저장된 컨텍스트 정보**를 이용할 수 없습니다.

이러한 이유로 클라이언트 응용 프로그램은 세션 상태를 완전히 유지해야 합니다.



#### 1.4. Cacheable

[Cacheable](https://restfulapi.net/caching/) 조건은 응답이 암시적 또는 명시적으로 캐시 가능 또는 캐시 불가능으로 스스로 레이블을 지정해야 함을 요구합니다.

응답이 캐시 가능한 경우 클라이언트 응용 프로그램은 나중에 동등한 요청 및 지정된 기간 동안 **응답 데이터를 재사용할 수 있는 권한을 얻습니다.**



#### 1.5. Layerd System

계층화된 시스템 스타일을 사용하면 구성 요소 동작을 제한하여 아키텍처가 계층적으로 구성될 수 있습니다.

예를 들어, 계층화된 시스템에서 각 구성 요소는 상호 작용하는 요소와 직접 연결되었는지 확인 할 수 없습니다.



#### 1.6. Code on Demand (Optional)

REST는 또한 [applet](https://ko.wikipedia.org/wiki/%EC%95%A0%ED%94%8C%EB%A6%BF) 또는 스크립트 형태의 코드를 다운로드하고 실행하여 클라이언트 기능을 확장할 수 있도록 합니다.

다운로드한 코드는 미리 구현해야 하는 기능의 수를 줄여 클라이언트를 단순화합니다. 서버는 클라이언트에 전달되는 기능의 일부를 코드 형태로 제공할 수 있으며 클라이언트는 코드를 실행하기만 하면 됩니다.



---



### **2. What is a Resource?**

REST에서 **정보를 추상화**하는 키가 리소스입니다. 이름을 지정할 수 있는 모든 정보는 리소스가 될 수 있습니다. 예를 들어 REST 리소스는 문서 또는 이미지, 임시 서비스, 다른 리소스 모음 또는 비가상 개체(예: 사람)일 수 있습니다.

특정 시간의 리소스 상태를 리소스 표현(resource representation)이라고 합니다.

리소스 표현은 다음으로 구성됩니다.

- 데이터
- 데이터를 설명하는 메타데이터
- 클라이언트가 원하는 다음 상태로 전환하는 데 도움이 될 수 있는 하이퍼미디어 링크.

> REST API는 상호 연결된 리소스의 조합으로 구성됩니다. 이 리소스 집합을 REST API의 리소스 모델이라고 합니다.

#### 2.1. 리소스 식별자

REST는 리소스 식별자를 사용하여 클라이언트와 서버 구성 요소 간의 상호 작용에 관련된 각 리소스를 식별합니다.



#### 2.2. 하이퍼미디어

표현( representation)의 데이터 형식은 미디어 유형으로 알려져 있습니다. 미디어 유형은 표현이 처리되는 방법을 정의하는 사양을 식별합니다.

RESTful API는 하이퍼텍스트처럼 보입니다. 주소를 지정할 수 있는 모든 정보 단위는 명시적으로(예: 링크 및 id 속성) 또는 암시적으로(예: 미디어 유형 정의 및 표현 구조에서 파생된) 주소를 전달합니다.

> 하이퍼텍스트(또는 하이퍼미디어)는 정보가 사용자(또는 자동 장치)가 선택을 얻고 행동을 선택하는 유도 장치가 되도록 정보와 제어를 동시에 표시하는 것을 의미합니다.
>
> 하이퍼텍스트는 브라우저에서 HTML(또는 XML 또는 JSON)일 필요가 없습니다. 기계는 데이터 형식과 관계 유형을 이해하면 링크를 따라갈 수 있습니다.
>
> — 로이 필딩



#### 2.3. 자기 설명 ( Self-Descriptive )

또한 리소스 표현은 **자체 설명적**이어야 합니다. 클라이언트는 리소스가 직원인지 장치인지 알 필요가 없습니다. **리소스와 연결된 미디어 유형에 따라 작동**해야 합니다.

따라서 실제로는 많은 사용자 지정 미디어 유형을 만들 것입니다. 일반적으로 하나의 리소스와 연결되는 것은 오직 하나의 미디어 유형입니다.

모든 미디어 유형은 기본 처리 모델을 정의합니다. 예를 들어, HTML은 하이퍼텍스트에 대한 렌더링 프로세스와 각 요소 주변의 브라우저 동작을 정의합니다.

> 미디어 유형은 GET/PUT/POST/DELETE/… 등등의 리소스 메소드와 관련이 없습니다.
>
> 단, 일부 미디어 유형 요소는 "href 속성이 있는 앵커 요소가 하이퍼텍스트 링크를 생성하는 하이퍼텍스트 링크를 생성합니다.
>
> CDATA로 인코딩된 href 속성에 해당하는 URI에서 검색 요청(GET)을 호출합니다."



---



### **3. Resource Methods**



REST와 관련된 또 다른 중요한 것은 **리소스 메서드**입니다. 이러한 리소스 메서드는 리소스의 두 상태 간에 원하는 전환을 수행하는 데 사용됩니다.

많은 사람들이 **리소스 메서드를 [HTTP 메서드](https://restfulapi.net/http-methods/)(예: GET/PUT/POST/DELETE)와 잘못 연결합니다.** Roy Fielding은 어떤 조건에서 어떤 방법을 사용해야 하는지에 대한 권장 사항을 언급한 적이 없습니다. 그가 강조하는 것은 인터페이스가 균일해야 한다는 것입니다.

예를 들어, **대부분의 사람들이 HTTP PUT을 권장하는 대신 애플리케이션 API가 리소스 업데이트에 HTTP POST를 사용하기로 결정했습니다.** 이건 괜찮습니다. 이렇게 해도 애플리케이션 인터페이스는 RESTful이라고 할 수 있습니다.

하지만 이상적으로는 리소스 상태를 전환하는 데 필요한 모든 것이 리소스 표현의 일부가 되어야 합니다. 여기에는 지원되는 모든 메서드와 표현을 날릴 형식이 포함됩니다.



> 초기 URI(책갈피) 및 의도된 청중(즉, API를 사용할 수 있는 모든 클라이언트가 이해할 것으로 예상되는)에 적합한 표준화된 미디어 유형 집합 외에는 사전 지식 없이 REST API를 입력해야 합니다.
>
> 그 시점부터 모든 애플리케이션 상태 전환은 수신된 표현에 존재하거나 해당 표현의 사용자 조작에 의해 암시되는 서버 제공 선택의 클라이언트 선택에 의해 구동되어야 합니다.
>
> 전환은 미디어 유형 및 리소스 통신 메커니즘에 대한 클라이언트의 지식으로 결정(또는 제한)될 수 있으며, 둘 다 즉석에서 개선될 수 있습니다(예: 주문형 코드). [여기서 실패는 대역 외 정보가 하이퍼텍스트 대신 상호 작용을 주도하고 있음을 의미합니다.]



---



### **4. REST != HTTP**

많은 사람들이 HTTP를 REST와 비교하는 것을 선호하지만. **REST와 HTTP는 동일하지 않습니다.**

REST도 웹(인터넷)을 보다 간소화되고 표준으로 만들려고 하지만 Roy Fielding은 **REST 원칙 사용을 보다 엄격하게 옹호합니다.** 그리고 이것은 사람들이 REST를 웹과 비교하기 시작하는 지점입니다.

Roy Fielding은 그의 논문에서 프로토콜 기본 설정이나 HTTP를 포함하여 구현 방향에 대해 언급한 적이 없습니다. 그때까지 우리는 우리의 인터페이스인 RESTful이라고 부를 수 있는 REST의 6가지 기본 원칙을 존중하고 있습니다.



---



### **5. 요약**

간단히 말해서 REST 아키텍처 스타일에서 데이터와 기능은 리소스로 간주되고 URI(Uniform Resource Identifier)를 사용하여 액세스됩니다.

리소스는 간단하고 잘 정의된 작업 집합을 사용하여 작동됩니다. 또한 클라이언트가 HTML, XML, 일반 텍스트, PDF, JPEG, JSON 등과 같은 다양한 형식의 콘텐츠에 액세스할 수 있도록 리소스를 해당 표현에서 분리해야 합니다.

클라이언트와 서버는 표준화된 인터페이스와 프로토콜을 사용하여 리소스 표현을 교환합니다. 일반적으로 HTTP가 가장 많이 사용되는 프로토콜이지만 REST는 이를 요구하지 않습니다.

리소스에 대한 메타데이터가 제공되어 캐싱을 제어하고 전송 오류를 감지하며 적절한 표현 형식을 협상하고 인증 또는 액세스 제어를 수행하는 데 사용됩니다.

그리고 가장 중요한 것은 서버와의 모든 상호 작용이 **상태 비저장(stateless)**이어야 한다는 것입니다.

이러한 모든 원칙은 RESTful 애플리케이션이 간단하고 가볍고 빠르도록 도와줍니다.

참조:

- [REST API는 하이퍼텍스트 기반이어야 합니다.](https://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven)

- [REST 아치 스타일](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)







## **REST Constraints**



REST는 Representational State Transfer의 약자로 Roy Fielding이 2000년에 만든 용어입니다. 네트워크를 통해 느슨하게 결합된 애플리케이션을 설계하기 위한 아키텍처 스타일로 웹 서비스 개발에 자주 사용됩니다.

REST는 하위 수준에서 어떻게 구현되어야 하는지에 대한 어떠한 규칙도 시행하지 않으며, 단지 높은 수준의 디자인 지침을 제시하고 우리가 직접 구현을 생각하도록 합니다.

마지막 직장에서 저는 2년 동안 주요 통신 회사를 위해 RESTful API를 설계했습니다. 이 게시물에서는 표준 디자인 관행과 별개로 내 생각을 공유할 것입니다. 당신은 몇 가지 점에서 나와 동의하지 않을 수 있지만 그것은 뭐 완전 괜찮습니다. 열린 마음으로 무엇이든 상담해 드리겠습니다.

'Roy Fielding'이 우리가 만들고자 하는 것이 무엇인지 명확하게 하기 위해 표준 디자인 관련 항목부터 시작하겠습니다. 그런 다음 RESTful API를 설계하는 동안 느낀 제 생각에 대해 논의할 것입니다.



### **1. REST 아키텍처 제약**

REST는 모든 웹 서비스를 진정한 RESTful API로 만드는 6가지 아키텍처 제약 조건을 정의합니다.



#### 1.1. Uniform interface

제약 조건 이름 자체가 적용되므로 API 소비자에게 노출되고 어김없이 따르는 시스템 내부 리소스에 대한 API 인터페이스를 결정해야 합니다. **시스템의 리소스에는 하나의 논리적 URI만 있어야 하며** 관련 데이터나 추가 데이터를 가져오는 방법을 제공해야 합니다. 리소스를 웹 페이지와 동의어화하는 것이 항상 더 좋습니다.

단일 리소스는 너무 커서는 안 되며 표현에 각각의 모든 것을 포함해야 합니다. 관련이 있을 때마다 리소스는 관련 정보를 가져오기 위해 **상대 URI를 가리키는 링크(HATEOAS)를 포함해야 합니다.**

또한 시스템 전체의 리소스 표현은 명명 규칙, 링크 형식 또는 데이터 형식(XML 또는/및 JSON)과 같은 특정 지침을 따라야 합니다.

**모든 리소스는 HTTP GET과 같은 일반적인 접근 방식을 통해 액세스할 수 있어야 하며** 일관된 접근 방식을 사용하여 유사하게 수정되어야 합니다.

> 개발자가 API 중 하나에 익숙해지면 다른 API에 대해서도 유사한 접근 방식을 따를 수 있어야 합니다.



#### 1.2. Client - Server

이 제약 조건은 본질적으로 **클라이언트 응용 프로그램과 서버 응용 프로그램이 서로에 대한 종속성 없이 개별적으로 발전**할 수 있어야 함을 의미합니다.

클라이언트는 리소스 URI만 알아야 하며 그게 전부입니다. 오늘날 이것은 웹 개발의 표준 관행이므로 사용자 측에서 화려한 것은 필요하지 않습니다. 간단하게 유지하십시오.

> 서버와 클라이언트는 인터페이스가 변경되지 않는 한 독립적으로 교체 및 개발될 수 있습니다.



#### 1.3. Stateless

Roy Fielding은 HTTP에서 영감을 얻었으므로 이 제약 조건을 반영합니다. 모든 클라이언트-서버 상호 작용을 stateless(상태 비저장)으로 만듭니다.

**서버는 클라이언트가 만든 최신 HTTP 요청에 대해 아무것도 저장하지 않습니다. 모든 요청을 새로운 것으로 취급합니다. 세션도, 히스토리도 없습니다.**

클라이언트 응용 프로그램이 최종 사용자를 위한 상태 저장 응용 프로그램이어야 하는 경우 사용자가 한 번 로그인하고 그 후에 다른 승인된 작업을 수행한다. 그러고나서 클라이언트의 각 요청에는 요청을 처리하는 데 필요한 모든 정보(인증 및 승인 세부 정보)를 포함한다.

> 클라이언트 컨텍스트는 요청 사이에 서버에 저장되지 않습니다. 클라이언트는 애플리케이션의 상태를 관리할 책임이 있습니다.



#### 1.4. Cacheable

오늘날의 세계에서 데이터 및 응답의 캐싱은 (적용 가능하거나 가능한 모든 곳에선) 가장 중요합니다. 여기서 읽고 있는 웹 페이지는 HTML 페이지의 캐시된 버전이기도 합니다.

**캐싱은 부하가 줄어들었기 때문에 클라이언트 측의 성능 향상과 서버의 확장성 범위를 향상시킵니다.**

REST에서 캐싱은 해당되는 경우 리소스에 적용되며 이러한 리소스는 캐시 가능하다고 반드시 선언해야 합니다. 캐싱은 서버 또는 클라이언트 측에서 구현할 수 있습니다.

> 잘 관리되는 캐싱은 일부 클라이언트-서버 상호 작용을 부분적으로 또는 완전히 제거하여 확장성과 성능을 더욱 향상시킵니다.



#### 1.5. Layered system

REST를 사용하면 **예를 들어 서버 A에 API를 배포하고 서버 B에 데이터를 저장하고 서버 C에서 요청을 인증하는 계층화된 시스템 아키텍처를 사용할 수 있습니다.**

클라이언트는 일반적으로 자신이 최종 서버에 직접 연결되어 있는지 중간 중간에 연결되어 있는지 알 수 없습니다.



#### 1.6. Code on demand (optional)

음, 이 제약 조건은 선택 사항입니다. 대부분의 경우 리소스의 정적 표현을 XML 또는 JSON 형식으로 보냅니다.

그러나 필요할 때 애플리케이션의 일부를 지원하기 위해 실행 가능한 코드를 자유롭게 반환할 수 있습니다. 예를 들어 클라이언트는 UI 위젯 렌더링 코드를 얻기 위해 API를 호출할 수 있습니다. 이것은 허용됩니다.

> 위의 모든 제약 조건은 진정한 RESTful API를 구축하는 데 도움이 되므로 이를 따라야 합니다. 그래도 때때로 한두 가지 제약 조건을 위반하는 자신을 발견할 수 있습니다. 하지만 걱정 하지 않아도 됩니다. 여전히 RESTful API 라고 할 수 있지만, "**진정한 RESTful**"은 아닙니다.



---

위의 모든 제약 조건은 WWW(웹)와 가장 밀접하게 관련되어 있습니다. RESTful API를 사용하면 웹 페이지에서 수행하는 것과 동일한 작업을 웹 서비스에서 수행할 수 있습니다.









## **Naming REST Resources**



### **1. What is a Resource?**

REST에서 기본 데이터 표현을 리소스라고 합니다.

**일관되고 강력한 REST 리소스 명명 전략을 갖는 것은 장기적으로 최고의 디자인 결정 중 하나임을 증명할 것입니다.**



> REST에서 핵심은 정보를 추상화하는 자원(리소스)입니다. 이름을 지정할 수 있는 모든 정보는 리소스가 될 수 있습니다. 문서 또는 이미지, 임시 서비스(예: "로스앤젤레스의 오늘 날씨"), 기타 리소스 모음, 비가상 객체(예: 사람) 등
>
> 즉, 저자의 하이퍼텍스트 참조 대상이 될 수 있는 모든 개념은 리소스 정의 내에 맞아야 합니다.
> 리소스는 특정 시점의 매핑에 해당하는 엔터티가 아니라 엔터티 집합에 대한 개념적 매핑입니다.
>
> — 로이 필딩의 논문



#### 1.1. 싱글톤 그리고 콜렉션 리소스

**리소스는 싱글톤 또는 컬렉션일 수 있습니다.**

예를 들어 보겠습니다. "`customers`"은 수집 리소스이고 "`customer`"은 싱글톤 리소스(뱅킹 도메인에서)입니다.

URI "`/customers`"를 사용하여 "`customers`" 컬렉션 리소스를 식별할 수 있습니다.

URI "``/customers/{customerId}``"를 사용하여 단일 "``customer``" 리소스를 식별할 수 있습니다.



#### 1.2. 컬렉션 및 하위 컬렉션 리소스

**리소스에는 하위 컬렉션 리소스도 포함될 수 있습니다.**

예를 들어 보겠습니다. 특정 "`customer`"의 하위 컬렉션 리소스 "`accounts`"는 URN "`/customers/{customerId}/accounts`"(은행 도메인에서)를 사용하여 식별할 수 있습니다.

마찬가지로 하위 컬렉션 리소스 "`accounts`" 내의 단일 리소스 "`account`"는 "`/customers/{customerId}/accounts/{accountId}`"와 같이 식별할 수 있습니다.



#### 1.3. URI

REST API는 URI(Uniform Resource Identifier)를 사용하여 리소스 주소를 지정합니다. REST API 설계자는 REST API의 리소스 모델을 API의 잠재적 클라이언트에 전달하는 URI를 만들어야 합니다.

리소스 이름이 잘 지정되면 API가 직관적이고 사용하기 쉽습니다. 제대로 수행되지 않으면 동일한 API를 사용하고 이해하기 어려울 수 있습니다.

> 균일한 인터페이스의 제약은 URI와 HTTP 동사의 조합과 표준 및 규칙에 따라 사용함으로써 부분적으로 해결됩니다.

다음은 새 API에 대한 리소스 URI를 생성할 때 도움이 되는 몇 가지 팁입니다.



---



### **2. 모범 사례**



#### 2.1. 리소스를 나타내기 위해 명사를 사용해라

RESTful URI는 **동작(동사)을 참조하는 대신 사물(명사)인 리소스를 참조해야 합니다.** 명사에는 동사가 갖지 않는 속성이 있기 때문입니다.

리소스의 몇 가지 예는 다음과 같습니다.

- Users of the system

- User Accounts

- Network Devices etc.

리소스 URI는 다음과 같이 설계할 수 있습니다 :

```
http://api.example.com/device-management/managed-devices 
http://api.example.com/device-management/managed-devices/{device-id} 
http://api.example.com/user-management/users
http://api.example.com/user-management/users/{id}
```

더 명확하게 하기 위해 리소스 원형을 네 가지 범주(문서, 컬렉션, 저장소 및 컨트롤러)로 나누겠습니다.

그런 다음 항상 **자원을 하나의 원형에 넣은 다음 명명 규칙을 일관되게 사용하는 것을 목표로 삼는 것이 가장 좋습니다.**

*균일성을 위해 **둘 이상의 원형이 혼합된 리소스를 설계하려는 유혹에 저항하십시오.***



##### 2.1.1. 문서

**문서 리소스는 개체 인스턴스 또는 데이터베이스 레코드와 유사한 단일 개념입니다.**

REST에서는 리소스 컬렉션 내의 단일 리소스로 볼 수 있습니다. 문서의 상태 표현에는 일반적으로 값이 있는 필드와 다른 관련 리소스에 대한 링크가 모두 포함됩니다.

**"단수" 이름을 사용하여 문서 리소스 원형을 나타냅니다.**

```
http://api.example.com/device-management/managed-devices/{device-id}
http://api.example.com/user-management/users/{id}
http://api.example.com/user-management/users/admin
```



##### 2.1.2. 컬렉션

컬렉션 리소스는 서버에서 관리하는 리소스 디렉터리입니다.

클라이언트는 컬렉션에 추가할 새 리소스를 제안할 수 있습니다. 그러나 새 리소스를 생성할지 여부를 선택하는 것은 컬렉션 리소스에 달려 있습니다.

컬렉션 리소스는 포함할 항목을 선택하고 포함된 각 리소스의 URI도 결정합니다.

**"복수" 이름을 사용하여 컬렉션 리소스 원형을 나타냅니다.**

```
http://api.example.com/device-management/managed-devices
http://api.example.com/user-management/users
http://api.example.com/user-management/users/{id}/accounts
```



##### 2.1.3. 저장소(store)

저장소(store)는 클라이언트 관리 리소스 저장소입니다.

저장소 리소스를 사용하면 API 클라이언트가 리소스를 넣고 다시 가져오고 삭제할 시기를 결정할 수 있습니다.

**저장소는 새 URI를 생성하지 않습니다.** 대신 저장된 각 리소스에는 URI가 있습니다. 리소스가 저장소에 처음 넣을 때 클라이언트가 URI를 선택했습니다.

**상점 리소스 원형을 나타내기 위해 "복수형" 이름을 사용하십시오.**

```
http://api.example.com/song-management/users/{id}/playlists
```



##### 2.1.4. 컨트롤러

컨트롤러 리소스는 절차적 개념을 모델링합니다. 컨트롤러 리소스는 매개 변수와 반환 값, 입력 및 출력이 있는 실행 가능한 함수와 같습니다.

**"동사"를 사용하여 컨트롤러 원형을 나타냅니다.**

```
http://api.example.com/cart-management/users/{id}/cart/checkout http://api.example.com/song-management/users/{id}/playlist/play
```





#### 2.2. 일관성이 핵심

모호성을 최소화하고 가독성과 유지 관리성을 극대화하기 위해 일관된 리소스 명명 규칙과 URI 형식을 사용합니다. 일관성을 유지하기 위해 아래 디자인 힌트를 구현할 수 있습니다.



##### 2.2.1. 슬래시(/)를 사용하여 계층적 관계를 나타냅니다.

슬래시(/) 문자는 리소스 간의 계층적 관계를 나타내기 위해 URI의 경로 부분에 사용됩니다. 예를 들어

```
http://api.example.com/device-management
http://api.example.com/device-management/managed-devices
http://api.example.com/device-management/managed-devices/{id}
http://api.example.com/device-management/managed-devices/{id}/scripts
http://api.example.com/device-management/managed-devices/{id}/scripts/{id}
```



##### 2.2.2. URI에 후행 슬래시(/)를 사용하지 마십시오.

URI 경로 내의 **마지막 문자인 슬래시(/)는 의미론적 값을 추가하지 않으며 혼동을 줄 수 있습니다.** URI에서 삭제하는 것이 좋습니다.

```
http://api.example.com/device-management/managed-devices/
http://api.example.com/device-management/managed-devices 	/*This is much better version*/
```



##### 2.2.3. 하이픈(-)을 사용하여 URI의 가독성 향상

사람들이 쉽게 스캔하고 해석할 수 있도록 URI를 만들려면 **하이픈(-) 문자를 사용**하여 긴 경로 세그먼트에서 이름의 가독성을 높이십시오.

```
http://api.example.com/device-management/managed-devices/
http://api.example.com/device-management/managed-devices 	/*This is much better version*/
```



##### 2.2.4. 밑줄( _ )을 사용하지 마십시오.

하이픈 대신 밑줄을 사용하여 구분 기호로 사용할 수 있습니다.

**그러나 응용 프로그램의 글꼴에 따라 밑줄(_) 문자가 일부 브라우저나 화면에서 부분적으로 가려지거나 완전히 숨겨질 수 있습니다._**

이러한 혼동을 피하려면 밑줄(_) 대신 하이픈(-)을 사용하십시오.



##### 2.2.5. URI에 소문자 사용

```
http://api.example.org/my-folder/my-doc       //1
HTTP://API.EXAMPLE.ORG/my-folder/my-doc     //2
http://api.example.org/My-Folder/my-doc       //3
```

위의 예에서 1과 2는 같지만 3은 My-Folder를 대문자로 사용하므로 그렇지 않습니다.



#### 2.3. 파일 확장자를 사용하지 마십시오

**파일 확장자는 보기에 좋지 않으며 어떤 이점도 주지 않습니다.** 이를 제거하면 URI 길이도 줄어듭니다. 그런데도 있을 이유가 없습니다.

위의 이유와는 별도로 파일 확장자를 사용하여 API의 미디어 유형을 강조하려면 Content-Type 헤더를 통해 전달되는 미디어 유형에 의존하여 본문의 콘텐츠를 처리하는 방법을 결정해야 합니다.



```
http://api.example.com/device-management/managed-devices.xml  /*Do not use it*/
http://api.example.com/device-management/managed-devices 	/*This is correct URI*/
```



#### 2.4. URI에 CRUD 함수 이름을 사용하지 마십시오.

**CRUD 기능을 나타내기 위해 URI를 사용해서는 안 됩니다.** URI는 리소스를 고유하게 식별하는 데만 사용되어야 하며 리소스에 대한 작업이 아닙니다.

어떤 CRUD 기능이 수행되는지 나타내기 위해 **HTTP 요청 방법을 사용해야 합니다.**

```
HTTP GET http://api.example.com/device-management/managed-devices  //Get all devices
HTTP POST http://api.example.com/device-management/managed-devices  //Create new Device

HTTP GET http://api.example.com/device-management/managed-devices/{id}  //Get device for given Id
HTTP PUT http://api.example.com/device-management/managed-devices/{id}  //Update device for given Id
HTTP DELETE http://api.example.com/device-management/managed-devices/{id}  //Delete device for given Id
```



#### 2.5. URI 컬렉션 필터링을 위해 쿼리 구성 요소를 사용하십시오.

특정 리소스 속성에 따라 정렬, 필터링 또는 제한되는 리소스 모음이 필요한 요구 사항이 자주 발생합니다.

**이 요구 사항의 경우 새 API를 생성하지 마십시오.** 대신 **리소스 컬렉션 API에서 정렬, 필터링 및 페이지 매김 기능을 활성화하고 입력 매개 변수를 쿼리 매개 변수로 전달하십시오.**



```
http://api.example.com/device-management/managed-devices
http://api.example.com/device-management/managed-devices?region=USA
http://api.example.com/device-management/managed-devices?region=USA&brand=XYZ
http://api.example.com/device-management/managed-devices?region=USA&brand=XYZ&sort=installation-date
```





# **2️⃣ Guides**



## **Caching**



### **1. 캐싱**

> 캐싱은 요청-응답 경로를 따라 여러 위치에 자주 액세스하는 데이터의 복사본을 저장하는 기능입니다.

소비자가 리소스 표현을 요청하면 요청은 캐시 또는 일련의 캐시 (로컬 캐시, 프록시 캐시 또는 역방향 프록시)를 통해 리소스를 호스팅하는 서비스로 이동합니다.

요청 경로에 있는 캐시에 요청된 표현의 새 복사본이 있는 경우 해당 복사본을 사용하여 요청을 충족합니다. 어떤 캐시도 요청을 만족시킬 수 없는 경우, 요청은 서비스(또는 공식적으로 알려진 오리진 서버)로 이동합니다.

HTTP 헤더를 사용하여 오리진 서버는 응답을 캐시할 수 있는지 여부와 캐시할 수 있는 경우, 사용자 및 기간을 나타냅니다. 응답 경로에 있는 캐시는 응답의 복사본을 가져올 수 있지만, 이는 캐싱 메타데이터가 허용하는 경우에만 가능합니다.

캐싱을 사용하여 네트워크를 최적화하면 다음과 같은 방식으로 전반적인 서비스 품질이 향상됩니다.

- 대역폭 줄이기
- 대기 시간 감소
- 서버의 부하 감소
- 네트워크 장애 숨기기



### **2. REST API의 캐싱**

캐시 가능하다는 것은 REST의 아키텍처 제약 조건 중 하나입니다.

- **GET** 요청은 특별한 조건이 발생할 때까지 기본적으로 캐시 가능해야 합니다. 일반적으로 브라우저는 모든 GET 요청을 캐시 가능한 것으로 처리합니다.
- **POST** 요청은 기본적으로 캐시할 수 없지만 명시적으로 캐싱을 허용하는 지시문이 있는 `Expires` 헤더 또는 `Cache-Control` 헤더가 응답에 추가되는 경우 캐시 가능하게 만들 수 있습니다.
- **PUT** 과 **DELETE** 요청에 대한 응답은 전혀 캐시할 수 없습니다.

> HTTP 날짜는 항상 현지 시간이 아닌 GMT로 표시됩니다.



### **3. 캐시 제어 헤더**

다음은 캐싱 동작을 제어하는 데 사용할 수 있는 주요 HTTP 응답 헤더입니다.



#### 3.1. Expires

Expires HTTP 헤더는 캐시된 표현의 절대 만료 시간을 지정합니다. 그 시간이 지나면 캐시된 표현은 오래된 것으로 간주되며 원본 서버에서 다시 확인해야 합니다.

표현이 만료되지 않음을 나타내기 위해 서비스에는 향후 최대 1년까지의 시간이 포함될 수 있습니다.

```
Expires: Fri, 20 May 2016 19:20:49 GMT
```



#### 3.2. Cache-Control

헤더 값은 쉼표로 구분된 하나 이상의 지시어로 구성됩니다. 이러한 지시어는 응답이 캐시 가능한지 여부, 캐시 가능한 경우 최대 사용 기간 또는 최대 사용 기간 등을 결정합니다.

```
Cache-Control: max-age=3600
```



#### 3.3. ETag

*ETag* 값은 서버가 수명 동안 리소스 상태를 고유하게 식별하기 위해 리소스와 연결하는 불투명 문자열 토큰입니다.

지정된 URL의 리소스가 변경되면 새 `Etag` 값을 생성해야 합니다. 이 둘을 비교하여 리소스의 두 표현이 동일한지 여부를 확인할 수 있습니다.

리소스를 요청하는 동안 클라이언트는 If-None-Match 헤더 필드의 ETag를 서버로 보냅니다. 서버가 요청된 리소스의 Etag와 If-None-Match 헤더로 전송된 값을 일치시킵니다.

두 값이 일치하는 경우 서버는 본문 없이 `304 Not Modified` 상태를 다시 보냅니다. 이 상태는 클라이언트에 응답의 캐시된 버전이 여전히 사용할 수 있음을 알려줍니다(새로 고침).

```
ETag: "abcd1234567n34jv"
```



#### 3.4. Last-Modified

응답의 Date 헤더는 응답이 생성된 시간을 나타내는 반면 Last-Modified 헤더는 연결된 리소스가 마지막으로 변경된 시간을 나타냅니다.

이 헤더는 리소스가 이전에 클라이언트 캐시에 저장된 리소스와 동일한지 확인하는 유효성 검사기로 사용됩니다. *ETag* 헤더보다 덜 정확하지만 폴백 메커니즘입니다.

Last-Modified 값은 Date 값보다 클 수 없습니다. Date 헤더는 [금지된 헤더 이름](https://fetch.spec.whatwg.org/#forbidden-header-name)에 나열됩니다.



``` 
Last-Modified: Fri, 10 May 2016 09:17:49 GMT
```







## **Compression**

REST API는 XML, JSON, HTML 또는 일반 텍스트와 같은 여러 형식으로 리소스 표현을 반환할 수 있습니다.

**이러한 모든 형식은 네트워크에서 대역폭을 절약하기 위해 더 적은 수의 바이트로 압축할 수 있습니다.**

다른 프로토콜은 다른 기술을 사용하여 압축을 활성화하고 클라이언트에 압축 체계에 대해 알려 클라이언트가 표현을 사용하기 전에 압축을 풀 수 있도록 합니다.



> 암호화와 같은 압축은 전송 중인 리소스 표현에 발생하며 클라이언트가 표현을 사용하기 전에 실행 취소해야 합니다.



HTTP는 REST에 대해 가장 널리 사용되는 프로토콜이므로 **HTTP 관련 응답 압축**의 예를 사용합니다.



### **1.압축 관련 요청/응답 헤더**



#### 1.1. Accept-Encoding (인코딩 수락)

리소스 표현을 요청하는 동안 - HTTP 요청과 함께 클라이언트는 클라이언트가 이해하는 압축 알고리즘의 종류를 나타내는 Accept-Encoding 헤더를 보냅니다.

`Accept-Encoding`의 두 가지 표준 값은 **compress**와 **gzip**입니다.

`Accept-encoding`의 헤더는 다음과 같습니다 :

```
GET        /employees         HTTP/1.1
Host:     www.domain.com
Accept:     text/html
Accept-Encoding:     gzip,compress
```



accept-encoding의 또 다른 사용 가능한 용도는 다음과 같습니다 :

```
Accept-Encoding: compress, gzip
Accept-Encoding:
Accept-Encoding: *
Accept-Encoding: compress;q=0.5, gzip;q=1.0
Accept-Encoding: gzip;q=1.0, identity; q=0.5, *;q=0
```

`Accept-Encoding` 필드가 요청에 있고 서버가 `Accept-Encoding` 헤더에 따라 허용 가능한 응답을 보낼 수 없는 경우, 서버는 **406(Not Acceptable)** 상태 코드로 오류 응답을 보내야 합니다.



#### 1.2. Content-Encoding

서버가 Accept-Encoding의 압축 알고리즘 중 하나를 이해하면 해당 알고리즘을 사용하여 표현을 제공하기 전에 압축할 수 있습니다.

성공적으로 압축되면 서버는 다른 HTTP 헤더(예: Content-Encoding)로 인코딩 체계를 클라이언트에 알립니다.

```
200 OK
Content-Type:     text/html
Content-Encoding:     gzip
```

요청 메시지의 엔티티 내용 코딩을 원본 서버에서 허용할 수 없는 경우, 서버는 상태 코드 **415(지원되지 않는 미디어 유형)**로 응답해야 합니다. 여러 내용 인코딩이 엔티티에 적용된 경우 모든 인코딩이 사용된 순서대로 나열되어야 합니다.

압축 요청 여부에 관계없이 요청 및 응답에 대한 원래 미디어 유형은 영향을 받지 않습니다.

압축은 추가 복잡성 없이 최소한의 비용으로 많은 대역폭을 절약할 수 있습니다. 또한 대부분의 웹 브라우저가 위의 헤더를 사용하여 웹 사이트 호스트 서버에서 압축된 표현을 자동으로 요청한다는 사실을 알고 있을 수 있습니다.

참조 :

- https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.11
- https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.3







## **Content Negotiation**



### **1. 콘텐츠 협상**

일반적으로 REST 리소스는 여러 개의 프레젠테이션을 가질 수 있는데, 이는 대부분 서로 다른 표현을 기대하는 클라이언트가 다를 수 있기 때문입니다.

**클라이언트가 적절한 프레젠테이션을 요청하는 것을 콘텐츠 협상이라고 합니다.**

> HTTP는 "컨텐츠 협상"을 위한 몇 가지 메커니즘에 대한 규정을 가지고 있습니다. 즉, 사용 가능한 여러 표현이 있을 때 주어진 응답에 대한 최상의 표현을 선택하는 프로세스입니다.



### **2. 서버 기반 vs 에이전트 기반 콘텐츠 협상**

응답에 대한 최상의 표현이 서버에 있는 알고리즘에 의해 선택되는 경우 이를 서버 주도 협상이라고 합니다. 에이전트 또는 클라이언트 측에서 선택하는 경우 에이전트 주도 협상이라고 합니다.

현실적으로 서버 측 협상의 용도는 많지 않을 것입니다. 그런 식으로, 우리는 고객의 기대에 대해 많은 가정을 해야 합니다.

클라이언트 컨텍스트 또는 클라이언트가 리소스 표현을 사용하는 방법과 같은 몇 가지 사항을 결정하는 것이 거의 불가능합니다. 또한 서버 주도 협상은 서버 측 코드를 불필요하고 복잡하게 만듭니다.

따라서 대부분의 REST API 구현은 에이전트 주도 콘텐츠 협상에 의존합니다. **에이전트 기반 콘텐츠 협상은 HTTP 요청 헤더 또는 리소스 URI 패턴의 사용에 따라 달라집니다.**



#### 2.1. HTTP 헤더 사용

서버 측에서 들어오는 요청에는 연결된 엔터티가 있을 수 있습니다. 유형을 결정하기 위해 서버는 HTTP 요청 헤더 `Content-Type`을 사용합니다.

콘텐츠 유형의 일반적인 예로는 "text/plain", "application/xml", "text/html", "application/json", "image/gif" 및 "image/jpeg"가 있습니다.

```
Content-Type: application/json
```



마찬가지로 클라이언트 측에서 원하는 표현 유형을 결정하기 위해 HTTP 헤더 **ACCEPT**가 사용됩니다. 위의 `Content-Type`에 대해 언급한 값 중 하나를 갖습니다.

```
Accept: application/json
```



일반적으로 요청에 `Accept` 헤더가 없으면 서버는 미리 구성된 기본 표현 유형을 보낼 수 있습니다.

> Accept 헤더 기반 콘텐츠 협상을 구현하는 것이 가장 많이 사용되고 권장되는 방법입니다.



#### 2.2. URL 패턴 사용

콘텐츠 유형 정보를 서버에 전달하는 또 다른 방법이 있습니다. 클라이언트는 리소스 URI의 특정 확장을 사용할 수 있습니다. 예를 들어 클라이언트는 다음을 사용하여 세부 정보를 요청할 수 있습니다.

```
http://rest.api.com/v1/employees/20423.xml
http://rest.api.com/v1/employees/20423.json
```

위의 경우 첫 번째 요청 URI는 두 번째 요청 URI가 JSON 응답을 반환할지 여부에 관계없이 XML 응답을 반환합니다.



### **3. 기본 설정 정의**

**상대 품질 요소인 'q' 매개변수를 사용하여 `Accept` 헤더에 여러 값을 가질 수 있습니다.**

클라이언트는 **클라이언트가 원하는 표현이 존재하는지 또는 그 당시 서버에서 지원하는지 확실하지 않을 때 수락 헤더에 여러 값을 제공하기를 원할 수 있습니다.**



예시

```
Accept: application/json,application/xml;q=0.9,*/*;q=0.8
```



위의 `Accept` 헤더를 사용하면 서버에 JSON 형식(첫 번째 선택)을 요청할 수 있습니다. 그렇지 않은 경우 XML 형식(두 번째 선택)을 반환할 수 있습니다. 여전히 가능하지 않은 경우 가능한 만큼 반환하도록 합니다.

기본 설정 순서는 0에서 1 사이의 값을 갖는 q 매개변수를 통해 정의됩니다. 아무 것도 지정하지 않으면 기본값은 1입니다.









## **HATEOAS**



### **1. HATEOAS 란?**

**HATEOAS(Hypermedia as Engine as Application State)**는 REST 애플리케이션 아키텍처의 제약 조건입니다. HATEOAS는 REST 스타일 아키텍처를 대부분의 다른 네트워크 애플리케이션 아키텍처에서 고유하게 유지합니다.

"**하이퍼미디어**" 라는 용어는 이미지, 영화 및 텍스트와 같은 다른 형태의 미디어에 대한 링크를 포함하는 모든 콘텐츠를 말합니다.

REST 아키텍처 스타일을 사용하면 API 응답 콘텐츠에서 하이퍼미디어 링크를 사용할 수 있습니다. 이를 통해 클라이언트는 하이퍼미디어 링크를 통과하여 적절한 리소스를 동적으로 탐색할 수 있습니다.

하이퍼미디어 링크를 탐색하는 것은 최종 목표를 달성하기 위해 관련 하이퍼링크를 클릭하여 웹 페이지를 탐색하는 것과 개념적으로 동일합니다.

예를 들어, 아래 JSON 응답은 HTTP GET http://api.domain.com/management/departments/10과 같은 API에서 제공될 수 있습니다.

```
{
    "departmentId": 10,
    "departmentName": "Administration",
    "locationId": 1700,
    "managerId": 200,
    "links": [
        {
            "href": "10/employees",
            "rel": "employees",
            "type" : "GET"
        }
    ]
}
```

앞의 예에서 서버가 반환한 응답에는 부서에 속한 직원을 읽기 위해 클라이언트가 탐색할 수 있는 직원 리소스 `10/employees`에 대한 하이퍼미디어 링크가 포함되어 있습니다.

위 접근 방식의 장점은 서버에서 반환된 하이퍼미디어 링크가 그 반대가 아니라 애플리케이션의 상태를 구동한다는 것입니다.

JSON에는 두 리소스 간의 링크를 나타내는 보편적으로 허용되는 형식이 없습니다. 응답 본문을 보내거나 HTTP 응답 헤더에 링크를 보내기로 결정할 수 있습니다.



```
HTTP/1.1 200 OK
...
Link: <10/employees>; rel="employees"
```



둘 다 좋은 해결책입니다.



### **2. HATEOAS 구현 방법**

우리는 웹사이트를 방문할 때 홈페이지를 방문합니다. 홈페이지는 일부 스냅샷과 웹사이트의 다른 섹션에 대한 링크를 제공합니다. 우리는 링크를 클릭하고 컨텍스트와 관련된 추가 정보 및 관련 링크를 얻습니다.

웹 사이트와 인간의 상호 작용과 마찬가지로 **REST 클라이언트는 초기 API URI에 도달하고 서버 제공 링크를 사용하여 필요한 리소스에 액세스하고 사용 가능한 작업을 동적으로 검색합니다.**

클라이언트는 서비스 또는 워크플로와 관련된 다양한 단계에 대한 사전 지식이 없어도 됩니다. **또한 클라이언트는 더 이상 다양한 리소스에 대한 URI 구조를 하드코딩할 필요가 없습니다.**

HATEOAS를 사용하면 클라이언트를 중단하지 않고 API가 발전함에 따라 서버가 URI를 변경할 수 있습니다.

위 API 상호작용은 HATEOAS를 통해서만 가능합니다.

각 REST 프레임워크는 프레임워크 기능을 사용하여 HATEOAS 링크를 생성하는 방법을 제공합니다. 예를 들어, Spring Boot HATEOAS 튜토리얼에서 링크는 리소스 상태로 클라이언트에 전송되는 리소스 모델 클래스의 일부입니다.



### **3. HATEOAS 참조**

다음은 JSON REST API 하이퍼미디어 링크를 지정하는 데 널리 사용되는 두 가지 형식입니다.



#### 3.1. RFC 5988(웹 링크)

RFC 5988은 웹상의 리소스 간의 관계를 정의하는 링크를 구축하기 위한 프레임워크를 제시합니다. RFC 5988의 각 링크에는 다음 속성이 포함됩니다.

- **Target URI(대상 URI)**: 각 링크에는 대상 IRI(Internationalized Resource Identifier)가 포함되어야 합니다. 이것은 `href` 속성으로 표현됩니다.
- **Link relation type(링크 관계 유형)**: 링크 관계 유형은 현재 컨텍스트(소스)가 대상 리소스와 어떻게 관련되어 있는지 설명합니다. 이것은 `rel` 속성으로 표시됩니다.
- **Attributes for target IRI(대상 IRI의 속성)**: 링크의 속성에는 `hreflang, media, title, type` 및 모든 확장 링크 매개변수가 포함됩니다.



#### 3.2. JSON 하이퍼미디어 API 언어(HAL)

JSON HAL은 링크와 같은 하이퍼미디어 제어를 JSON 또는 XML로 표현하기 위한 규칙을 설정하는 유망한 제안입니다. 현재 초안 단계에 있습니다.

연결된 두 MIME 유형은 다음과 같습니다.

```
media type: application/hal+xml 
media type: application/hal+json
```

HAL의 각 링크에는 다음 속성이 포함될 수 있습니다.

- **Target URI(대상 URI)**: 대상 리소스 URI를 나타냅니다. 이것은 `href` 속성으로 표현됩니다.
- **Link relation(링크 관계):**링크 관계 유형은 현재 컨텍스트가 대상 리소스와 관련되는 방식을 설명합니다. 이것은 `rel` 속성으로 표시됩니다.
- **Type(유형):**예상 리소스 미디어 유형을 나타냅니다. 이것은 `type` 속성으로 표현됩니다.



우리 응용 프로그램에 대한 하이퍼 미디어 링크 형식을 선택하는 데 옳고 그름이 없습니다. 대부분의 사용 사례 요구 사항을 충족하는 형식을 선택하고 고수해야 합니다.









## **Idempotence**



### **1. APIs의 멱등**

**REST API의 맥락에서, 동일한 요청을 여러 개 만드는 것이 단일 요청을 만드는 것과 동일한 효과를 가진다면, 우리는 그것을 멱등하다고 할 수 있습니다.**

우리가 REST API를 설계할 때, 우리는 API 소비자가 실수를 할 수 있다는 것을 염두에 두어야 합니다.소비자는 API를 중복 요청이 있을 수 있는 방식으로 클라이언트 코드를 작성할 수 있습니다.

이러한 중복 요청은 의도적일수도 그렇지 않을 수도 있습니다. (예: 시간 초과 또는 네트워크 문제).중복 요청이 시스템을 불안정하게 만들지 않도록 API를 결함 허용시스템으로 만들어야 합니다.



>  멱등한 HTTP 메서드는 다른 결과 없이 여러 번 호출할 수 있는 방법입니다. 메서드가 한 번만 호출되었는지, 아니면 열 번 호출되었는지는 중요하지 않습니다. 결과는 항상 같아야 합니다.
>
>  멱등은 성공적으로 수행된 요청의 결과가 실행되는 횟수와 무관하다는 것을 의미합니다.
>
>  예를 들어, 산술연산에서 숫자에 0을 더하는 것은 멱등이 성립하는 연산입니다.



### **2. HTTP 메서드의 멱등성**

API를 설계할 때 REST 원칙을 따르면 GET, PUT, DELETE, HEAD, OPTIONS 및 TRACE 메서드에 대해 자동으로 멱등한 REST API를 갖게 되고, POST API만 멱등이 성립하지 않습니다.

1. `POST`는 멱등이 아니다.
2. `GET, PUT, DELETE, HEAD, OPTIONS` 및 `TRACE`는 멱등이다.

POST를 제외한 위의 HTTP 메서드가 어떻게 멱등이 성립하는지, 그리고 POST는 왜 멱등하지 않는지 이유를 분석해보자.



#### 2.1. HTTP POST

일반적으로, 꼭 그렇지는 않지만, `POST` API는 서버에 새 리소스를 만드는 데 사용됩니다.

따라서 동일한 POST 요청을 N번 호출하면 서버에 N개의 새로운 리소스가 생성됩니다. **그래서 POST는 멱등하지 않습니다.**



#### 2.2. HTTP GET, HEAD, OPTIONS 및 TRACE

`GET, HEAD, OPTIONS` 및 `TRACE` 메서드는 서버의 리소스 상태를 변경하지 않습니다.

순전히 해당 시점의 리소스 표현 또는 메타데이터를 검색하기 위한 것입니다.

따라서 여러 요청을 호출하면 서버에서 쓰기 작업이 수행되지 않으므로 **GET, HEAD, OPTIONS 및 TRACE 메서드는 멱등합니다.**



#### 2.4 HTTP DELETE



##### 2.4.1. 리소스 식별자를 사용하여 삭제

N개의 유사한 `DELETE` 요청을 호출하면 첫 번째 요청이 리소스를 삭제하고 응답은 `200(OK)` 또는 `204(내용 없음)`가 됩니다.

다른 N-1 요청은 404(Not Found)를 반환합니다.

이 때의 응답은 첫 번째 요청과는 확실히 다르지만, 원본 리소스가 이미 삭제되었기 때문에 서버 측 리소스에 대한 상태 변경은 없습니다.

**따라서 DELETE는 멱등합니다.**



##### 2.4.2. 리소스 식별자 없이 삭제

일부 시스템에 다음과 같은 DELETE API가 있을 수 있습니다.

```
DELETE/item/last
```

위의 경우 작업을 N번 호출하면 N개의 리소스가 삭제됩니다. **DELETE는 이 경우 멱등하지 않습니다.**

이 경우, POST는 멱등하지 않기 때문에 위의 API를 POST로 변경하는 것이 나을 것입니다.

```
POST/item/last
```

이제 이것은 HTTP 사양에 더 가깝고, 따라서 REST와 더 호환됩니다.



---



## **Security Essentials**

REST API 보안은 사후 고려 사항이 아닙니다. 모든 개발 프로젝트와 REST API의 필수적인 부분이 되어야 합니다.

기본 인증, OAuth 등과 같이 RESTful API를 보호하는 여러 가지 방법이 있지만, 한 가지 확실한 것은 RESTful API가 무상태 프로토콜이어야 한다는 것입니다.

따라서 요청 인증/허가는 세션에 의존해서는 안 됩니다.대신 각 API 요청에는 모든 요청에 대해 서버에서 검증해야 하는 일종의 인증 자격 증명이 함께 제공되어야 합니다.



### **1. REST 보안 설계 원칙**

Jerome Saltzer와 Michael Schroeder의 "컴퓨터 시스템에서의 정보 보호"라는 논문에선 아래와 같이 컴퓨터 시스템의 정보 보안을 위한 8가지 설계 원칙을 제시했습니다.

- **Least Privilege(최소 권한)**: 엔티티는 권한이 부여된 작업을 수행하는 데 필요한 사용 권한 집합만 가지고 있어야 합니다. 사용 권한은 필요에 따라 추가할 수 있으며 더 이상 사용하지 않을 때는 취소해야 합니다.
- **Fail-Safe Defaults**: 시스템 내 리소스에 대한 사용자의 기본 액세스 수준은 명시적으로 "허가"가 부여되지 않은 경우 "거부"되어야 합니다.
- **The economy of Mechanism**: 디자인은 가능한 한 단순해야 합니다. 모든 구성 요소 인터페이스와 이들 간의 상호 작용은 이해하기 쉬워야 합니다.
- **Complete Mediation**: 시스템은 모든 리소스에 대한 액세스 권한을 검증하여 해당 리소스가 허용되고 캐시된 권한 매트릭스에 의존하지 않도록 해야 합니다. 주어진 리소스에 대한 액세스 수준이 취소되고 있는데 해당 수준이 사용 권한 매트릭스에 반영되지 않으면 보안을 위반하게 될 것입니다.
- **open Design(열린 설계)**: 이 원칙은 신뢰성있는 알고리즘 없이 개방적인 방식으로 시스템을 구축하는 것의 중요성을 강조합니다.
- **Separation of Privilege(권한 분리)**: 엔티티에 사용 권한을 부여하는 것이 단순히 단일 조건을 기반으로 해서는 안 됩니다. 리소스 유형에 따라 조건을 조합하는 것이 더 좋습니다.
- **Least Common Mechanism**: 이는 여러 구성 요소 간에 상태를 공유하는 것의 위험성에 관한 것입니다. 만약 하나의 요소가 공유 상태를 손상시킬 수 있는 경우 공유 상태에 의존하는 다른 모든 구성 요소를 손상시킬 수 있습니다.
- **Psychological Acceptability**: 보안 메커니즘은 보안 메커니즘이 없는 경우보다 리소스 접근을 더 어렵게 해서는 안 된다고 명시되어 있습니다. 간단히 말해서, 보안은 사용자 경험을 악화시켜서는 안 됩니다.





### **2. REST API 보안의 모범 사례**

아래는 REST API의 보안 메커니즘을 설계하기 위한 체크리스트 역할을 할 수 있습니다.



#### 2.1. Keep it Simple

API/시스템 보안 – 보안이 얼마나 필요한지를 확인해야 합니다. 솔루션을 "불필요하게" 더 복잡하게 만들 때마다 구멍을 남길 가능성이 높습니다.



#### 2.2. Always Use HTTPS

항상 SSL을 사용하면 인증 자격 증명을 임의로 생성된 액세스 토큰으로 단순화할 수 있습니다. 토큰은 HTTP Basic Auth의 username 필드에 전달됩니다.

비교적 사용이 간편하고 많은 보안 기능을 무료로 이용할 수 있습니다.HTTP 2를 사용하는 경우 성능 향상을 위해 단일 연결을 통해 여러 요청을 보낼 수도 있으므로 이후 요청에 대한 전체 TCP 및 SSL 핸드셰이크 오버헤드를 방지할 수 있습니다.



#### 2.3. Use Password Hash

일부 해킹 시도에서 암호가 손상되더라도 시스템을 보호하거나 손상을 최소화하기 위해 암호를 항상 해시화해야 합니다.

PBKDF2, bcrypt 및 scrypt 알고리즘과 같이 암호 보안에 매우 효과적인 해싱 알고리즘이 많이 있습니다.



#### 2.4. Never expose information on URLs

사용자 이름, 암호, 세션 토큰 및 API 키는 웹 서버 로그에 캡처되어 쉽게 악용될 수 있으므로 URL에 나타나지 않아야 합니다.



```
https://api.domain.com/user-management/users/{id}/someAction?apiKey=abcd123456789
//매우 나쁜 경우입니다.
```

위의 URL은 API 키를 표시합니다. 따라서 이런 형태의 보안은 절대 사용하지 마십시오.



#### 2.5. Consider OAuth

기본 인증은 대부분의 API에 충분하고 올바르게 구현될 경우 안전하지만 OAuth도 고려해 보는 것이 좋습니다.

OAuth 2.0 인증 프레임워크를 사용하면 리소스 소유자와 HTTP 서비스 간의 승인 상호 작용을 조정하거나 타사 응용 프로그램이 HTTP 서비스에 대한 액세스를 대신하도록 허용하여 타사 응용 프로그램이 HTTP 서비스에 대한 제한된 액세스 권한을 얻을 수 있습니다.



#### 2.6. Consider Adding Timestamp in Request

다른 요청 매개 변수와 함께 API 요청에서 요청 타임스탬프를 HTTP 사용자 정의 헤더로 추가할 수 있습니다.

서버는 현재 타임스탬프를 요청 타임스탬프와 비교하고 적절한 시간(30초 정도)이 지난 경우에만 요청을 수락합니다.

이렇게 하면 이 타임스탬프를 변경하지 않고 시스템을 강제로 재생하려는 사용자의 매우 기본적인 재생 공격을 방지할 수 있습니다.





#### 2.7. Input Parameter Validation

요청 매개 변수가 애플리케이션 로직에 도달하기 전에 첫 번째 단계에서 유효성을 검사합니다.

강력한 유효성 검사를 수행하고 유효성 검사에 실패하면 요청을 즉시 거부합니다.





## **Versioning**

버전에 대한 어려움을 해결하고 싶다면 버전 관리를 하십시오.

버전 관리를 통해 API에서 필요한 변경 사항이 생기면 더 빨리 반복(대응)할 수 있습니다.

> 시스템에 대한 지식과 경험이 향상됨에 따라 API의 변화는 불가피합니다. 기존 client integration이 중단될 위험이 있는 경우 이러한 변경의 영향을 관리하는 것이 어려울 수 있습니다.



### **1. When to version?**

**API는 변경 사항이 있을 때만 업그레이드하면 됩니다.**

변경 사항에는 다음이 포함됩니다.

- 하나 이상의 호출에 대한 응답 데이터 형식의 변경
- 요청 또는 응답 유형의 변경(ex. 정수를 부동 소수점으로 변경)
- API의 모든 부분을 제거하는 경우

> **연속적인 변경 사항**은 항상 API 또는 컨텐츠 응답 유형의 major 버전 번호의 변경을 발생시킵니다.
>
> 새 엔드포인트 또는 새 응답 매개 변수 추가와 같이 **중단되지 않는 변경사항**은 major 버전 번호를 변경할 필요가 없습니다.

그러나 캐시된 버전의 데이터를 수신, 혹은 다른 API 문제가 발생할 수 있는 소비자를 위해 변경한 경우, API의 minor 버전을 추적하는 것은 도움이 될 수 있습니다.





### **2. REST API를 어떻게 버전관리 하는가?**

REST는 특정 버전 관리 지침을 제공하지 않지만 일반적으로 사용되는 접근 방식은 다음과 같이 세 가지 범주로 나뉩니다.



#### 2.1. URI Versioning

URI를 사용하는 것은 URI가 고유한 리소스를 참조해야 한다는 원칙에 위배되지만 가장 직접적인 접근 방식(그리고 가장 일반적으로 사용됨)입니다. 또한 버전이 업데이트될 때 client integration이 중단됩니다.

```
http://api.example.com/v1
http://apiv1.example.com
```

버전은 숫자일 필요도 없고 "v[x]" 구문을 사용하여 지정할 수도 없습니다.

대안으로는 날짜, 프로젝트 명, 시즌 또는 API를 제작하는 팀에게 충분히 의미 있고 버전이 변경됨에 따라 유연하게 작용할 수 있는 기타 식별자가 있습니다.



#### 2.2. 사용자 정의 요청 헤더를 사용한 버전 관리

사용자 정의 헤더(예: Accept-version)를 사용하면 기존 Accept 헤더에 의해 구현된 content negotiation(클라이언트가 선호하는 표현 요청)과 중복되지만 버전 간의 URI를 보존할 수 있습니다.

```
Accept-version: v1
Accept-version: v2
```



#### 2.3. "Accept" 헤더를 사용한 버전관리

Content negotiation을 통해 URL을 깔끔하게 보존할 수 있지만, 다른 버전의 content를 제공해야 하는 어려움을 해결해야 합니다.

이러한 어려움은 전송할 리소스 버전을 결정하는 역할을 하는 API 컨트롤러에게 그 책임을 가중하는 경향이 있습니다.

이렇게 되면, 클라이언트는 리소스를 요청하기 전에 어떤 헤더를 지정해야 하는지 알아야 하기 때문에 더 복잡한 API가 되는 경향이 있습니다.

```
Accept: application/vnd.example.v1+json
Accept: application/vnd.example+json;version=1.0
```

현실 세계에서 API는 완전하게 안정적이지 않을 것입니다.

따라서 이러한 변경 사항을 어떻게 관리하는지가 중요합니다.잘 문서화되어 있고 점진적으로 사장되고 있는 API는 대부분의 API에서 허용 가능합니다.



## **Statelessness**



### **1. 무상태성**

REST(Respresentational "**State**" Transfer) 아키텍처에 따르면 서버는 클라이언트의 상태를 서버 측에 저장하지 않습니다. 이것을 무상태성이라 합니다.

클라이언트에서 서버로 보내는 각 요청에는 요청을 이해하는 데 필요한 모든 필수 정보가 포함되어야 합니다. 서버에서 서버에 저장된 context(환경설정 정보)를 사용할 수 없습니다.

따라서 응용프로그램의 세션 상태는 전적으로 클라이언트에 의해 유지됩니다. **고객은 세션 관련 정보를 자체적으로 저장하고 처리할 책임이 있습니다. **

이는 필요할 때마다 서버로 상태 정보를 전송할 책임이 클라이언트에게 있음을 의미합니다. 클라이언트와 서버 사이에 세션 선호도 또는 고정 세션이 없어야 합니다.



> 무상태성은 모든 HTTP 요청은 완전히 격리되어 발생함을 의미합니다. 클라이언트가 HTTP 요청을 진행하면, 서버가 요청을 수행하는 데 필요한 모든 정보가 포함됩니다.
>
> 서버는 클라이언트의 이전 요청 정보에 의존하지 않습니다. 이전 정보가 중요한 경우 클라이언트는 해당 정보를 현재 요청의 일부로 보냅니다.



클라이언트가 이러한 무상태성 API에 접근할 수 있도록 하려면 서버 또한 클라이언트가 상태를 생성/유지하는 데 필요한 모든 정보를 포함해야 합니다.

무상태성이 되기 위해 클라이언트의 인증/승인 세부 정보조차 저장하지 마십시오. 대신 각 요청에 authentication credentials(인증 자격 증명)을 제공하십시오.

따라서 각 요청은 독립적이어야 하며 이전에 동일한 클라이언트와 주고받은 것에 의해 영향을 받지 않아야 합니다.



### **2. Application State vs Resource State**

Application state와 resource state는 완전히 다른 내용이므로 이를 이해하는 것은 중요합니다.

Application state는 서버가 클라이언트의 요청, 이전 상호 작용의 세부 정보 및 현재의 환경설정 정보를 식별하기 위해 저장하는 "서버 측 데이터"입니다.

Resource state는 서버에 있는 리소스의 현재 상태이며, 클라이언트와 서버 간의 상호 작용과는 관련이 없습니다. 이것은 서버로부터 API 응답으로 받는 것입니다. 우리는 그것을 resource representation이라고 부릅니다.

> 즉, REST 무상태성이란 application 상태로부터의 자유로운 상태를 의미합니다.



### **3. 무상태 API의 장점**

REST API가 무상태성이면 몇 가지 눈에 띄는 이점이 있습니다.

1. 무상태성은 API를 여러 서버에 배포하여 수백만 명의 동시 사용자로 확장(스케일 아웃)하는 데 도움이 됩니다. 세션 관련 종속성이 없으므로 어떤 서버든 요청을 처리할 수 있습니다.
2. 무상태성은 server-side state synchronization logic을 모두 제거하여 REST API의 복잡성을 줄일 수 있습니다.
3. 무상태성 API는 캐시하기도 쉽습니다. 특정 소프트웨어가 요청 하나를 보고 HTTP 요청 결과를 캐시할지 여부를 결정할 수 있습니다. 왜냐하면, 이전 요청의 상태가 이 요청의 캐시 가능성에 영향을 미칠 수 있다는 지속적인 불확실성은 없기 때문입니다. 이는 애플리케이션의 성능을 향상시킵니다.
4. 클라이언트는 요청마다 필요한 모든 정보를 보내기 때문에 서버는 응용프로그램에서 각 클라이언트가 "어디" 있는지 추적하지 않아도 됩니다.







# **3️⃣ Tech - How To**



## **Designing REST APIs**



REST를 개별적으로 학습하는 것과 이러한 모든 개념을 실제 애플리케이션 개발에 적용하는 것은 완전히 다른 문제입니다.

이 튜토리얼 에서는 네트워크 기반 애플리케이션을 위한 **REST API를 설계하는** 방법을 가르쳐줄 것입니다. 이 예제를 통해 응용 프로그램 설계 프로세스에서 REST 원칙을 적용하는 방법을 학습할 것입니다.



### **1. 리소스 식별 – 객체 모델링**

REST API 기반 애플리케이션을 디자인하는 첫 번째 단계는 **리소스** 로 표시될 개체를 식별하는 것입니다.

네트워크 기반 응용 프로그램의 경우 개체 모델링이 훨씬 더 간단합니다. *장치, 관리되는 엔티티, 라우터, 모뎀* 등과 같은 많은 것들이 있습니다. 단순화를 위해 두 가지 리소스만 고려합니다.

- 장치(Devices)
- 구성(Configurations)

여기서 구성은 장치의 하위 리소스일 수 있습니다. 장치에는 많은 구성 옵션이 있을 수 있습니다.

위 모델의 두 객체/자원 모두 정수(Integer)의 `id` 속성인 고유 식별자를 가지고 있습니다.



### **2. 모델 URI 생성**

이제 개체 모델이 준비되면, 리소스 URI를 결정할 차례입니다. 이 단계에서 리소스 URI를 설계하는 동안 리소스와 하위 리소스 간의 관계에 중점을 둡니다.

이러한 **리소스 URI는 API의 endpoint입니다**.우리 애플리케이션에서 디바이스는 최상위 리소스입니다. 그리고 구성은 장치 아래의 하위 리소스입니다. 아래의 URI를 적어 봅시다.



```
/devices
/devices/{id}
 
/configurations
/configurations/{id}
 
/devices/{id}/configurations
/devices/{id}/configurations/{configId}
```

이러한 **URI에는 동사나 연산을 사용하지 않습니다**. URI에 동사를 포함하지 않는 것이 중요합니다. **URI는 모두 명사여야 합니다**.



### **3. 리소스 Representations 결정**

이제 리소스 URI가 결정되면 해당 representation에 대해 작업해 보겠습니다. 대부분의 표현은 [XML 또는 JSON](https://restfulapi.net/content-negotiation/) 형식으로 정의됩니다. 데이터가 구성되는 방식을 더 잘 표현하는 XML 예제를 보겠습니다.



#### 3.1. 장치의 수집 자원

컬렉션 리소스를 반환할 때, 해당 리소스에 대한 가장 중요한 정보만 포함합니다. 이렇게 하면 응답 페이로드의 크기가 줄어들게되고, 이건 **API의 성능**을 향상시킵니다.

```
<devices size="2">
 
  	<link rel="self" href="/devices"/>
 
  	<device id="12345">
  	  	<link rel="self" href="/devices/12345"/>
  	  	<deviceFamily>apple-es</deviceFamily>
  	  	<OSVersion>10.3R2.11</OSVersion>
  	  	<platform>SRX100B</platform>
  	  	<serialNumber>32423457</serialNumber>
  	  	<connectionStatus>up</connectionStatus>
  	  	<ipAddr>192.168.21.9</ipAddr>
  	  	<name>apple-srx_200</name>
  	  	<status>active</status>
  	</device>
 
  	<device id="556677">
  	  	<link rel="self" href="/devices/556677"/>
  	  	<deviceFamily>apple-es</deviceFamily>
  	  	<OSVersion>10.3R2.11</OSVersion>
  	  	<platform>SRX100B</platform>
  	  	<serialNumber>6453534</serialNumber>
  	  	<connectionStatus>up</connectionStatus>
  	  	<ipAddr>192.168.20.23</ipAddr>
  	  	<name>apple-srx_200</name>
  	  	<status>active</status>
  	</device>
 
</devices>
```



#### 3.2. 단일 장치 리소스

컬렉션 URI와 달리, 단일 리소스 URI에는 특정 장치에 대한 완전한 정보가 포함됩니다. 여기에는 하위 리소스 및 기타 지원되는 작업에 대한 링크 목록도 포함합니다. 이렇게 하면 REST API [HATEOAS](https://restfulapi.net/hateoas/) 구동이 가능합니다.

```
<device id="12345">
  	<link rel="self" href="/devices/12345"/>
 
  	<id>12345</id>
  	<deviceFamily>apple-es</deviceFamily>
  	<OSVersion>10.0R2.10</OSVersion>
  	<platform>SRX100-LM</platform>
  	<serialNumber>32423457</serialNumber>
  	<name>apple-srx_100_lehar</name>
  	<hostName>apple-srx_100_lehar</hostName>
  	<ipAddr>192.168.21.9</ipAddr>
  	<status>active</status>
 
  	<configurations size="2">
  	  	<link rel="self" href="/configurations" />
 
  	  	<configuration id="42342">
        	  	<link rel="self" href="/configurations/42342" />
  	  	</configuration>
 
  	  	<configuration id="675675">
        	  	<link rel="self" href="/configurations/675675" />
  	  	</configuration>
  	</configurations>
 
  	<method href="/devices/12345/exec-rpc" rel="rpc"/>
  	<method href="/devices/12345/synch-config"rel="synch device configuration"/>
</device>
```



#### 3.3. 구성의 컬렉션 리소스

장치 컬렉션의 표현과 마찬가지로 최소한의 정보만으로 구성 컬렉션의 representation을 만듭니다.

```
<configurations size="20">
  	<link rel="self" href="/configurations" />
 
  	<configuration id="42342">
  	  	<link rel="self" href="/configurations/42342" />
  	</configuration>
 
  	<configuration id="675675">
  	  	<link rel="self" href="/configurations/675675" />
  	</configuration>
  	...
  	...
</configurations>
```

내부 configurations컬렉션 표현 device은 최상위 configurationsURI와 유사합니다.

유일한 차이점은 장치에 대한 구성이 두 개뿐이므로 두 개의 구성 항목만 장치 아래에 하위 리소스로 나열된다는 것입니다.



#### 3.4. 단일 구성 리소스

이제 단일 구성 리소스 representation에는 관련 링크를 포함하여 이 리소스에 대한 모든 가능한 정보가 있어야 합니다.

```
<configuration id="42342">
  	<link rel="self" href="/configurations/42342" />
  	<content><![CDATA[...]]></content>
  	<status>active</status>
  	<link rel="very big raw configuration script" href="/configurations/42342/raw" />
</configuration>
```



#### 3.5. 단일 장치에서 구성 리소스 수집

이 구성의 하위 컬렉션은 구성의 기본 컬렉션의 하위 집합이며 장치에만 고유하게 해당됩니다.

기본 컬렉션의 하위 집합이므로 기본 컬렉션과 **다른 representation 데이터 필드를 생성하지 마십시오** . 기본 컬렉션과 동일한 프레젠테이션 필드를 사용합니다.

```
<configurations size="2">
  	<link rel="self" href="/devices/12345/configurations" />
 
  	<configuration id="53324">
  	  	<link rel="self" href="/devices/12345/configurations/53324" />
  	  	<link rel="detail" href="/configurations/53324" />
  	</configuration>
 
  	<configuration id="333443">
  	  	<link rel="self" href="/devices/12345/configurations/333443" />
  	  	<link rel="detail" href="/configurations/333443" />
  	</configuration>
</configurations>
```

이 하위 리소스 컬렉션에는 두 개의 링크가 있습니다. 하나는 하위 컬렉션 내에서 직접 표현(representation)하기 위한 것이고, 다른 하나는 **기본 컬렉션**/devices/12345/configurations/333443 에서 위치를 가리키는, /configurations/333443 입니다.더 특별한 방식으로 장치 별 구성에 대한 액세스를 제공할 수 있으므로 두 개의 링크가 필수적입니다.

또한 일부 필드를 마스킹하는 기능 (디자인에 필요한 경우)이 있으며 **보조 컬렉션**에는 표시되지 않습니다.



#### 3.6. 단일 장치 아래의 단일 구성 리소스

이 representation은 기본 컬렉션의 구성 표현과 거의 유사한 representation을 가져야 하거나, 몇 개의 필드를 마스킹할 수 있습니다.

이 하위 리소스 representation에는 기본 프레젠테이션에 대한 추가적인 링크도 있습니다.

```
<configuration id="11223344">
  	<link rel="self" href="/devices/12345/configurations/11223344" />
  	<link rel="detail" href="/configurations/11223344" />
  	<content><![CDATA[...]]></content>
  	<status>active</status>
  	<link rel="raw configuration content" href="/configurations/11223344/raw" />
</configuration>
```

이제 다음 섹션으로 넘어가기 전에 몇 가지 관찰 사항을 기록해 둡시다.

- 리소스 URI는 모두 명사입니다.
- URI는 일반적으로 리소스 모음과 단일 리소스의 두 가지 형태입니다.
- 컬렉션은 1차 컬렉션과 2차 컬렉션의 두 가지 형태가 있습니다. 보조 컬렉션은 기본 컬렉션의 하위 컬렉션입니다.
- 각 리소스/컬렉션에는 적어도 하나의 링크, 즉 자신에 대한 링크가 포함되어 있습니다.
- 컬렉션에는 리소스에 대한 가장 중요한 정보만 포함되어 있습니다.
- 리소스에 대한 완전한 정보를 얻으려면 특정 리소스 URI에만 액세스해야 합니다.
- Representation에는 추가 링크가 있을 수 있습니다(즉, 단일 장치의 메서드). 여기서 메소드는 POST 메소드를 나타냅니다. 또한 완전히 새로운 방식으로 더 많은 속성이나 양식 링크를 설정할 수 있습니다.
- 우리는 아직 이러한 리소스에 대한 작업에 대해 이야기하지 않았습니다.



### **4. HTTP 메소드 할당**

따라서 리소스 URI와 해당 representation이 이제 수정되었습니다. 모든 응용 프로그램의 가능한 작업을 결정하고 해당 작업을 리소스 URI에 매핑하겠습니다.

예를 들어, 당사 네트워크 애플리케이션의 사용자는 네트워크에서 장치를 검색, 생성, 업데이트 또는 삭제할 수 있고 장치 구성을 생성/배포/제거할 수 있습니다. 따라서 이러한 작업을 해당 리소스에 할당해 보겠습니다.





#### 4.1. 모든 장치 또는 구성 탐색(Browse) [기본 컬렉션]

```
HTTP GET /devices
HTTP GET /configurations
```



컬렉션 크기가 큰 경우 페이징 및 필터링을 적용할 수도 있습니다. 예를 들어, 아래 요청은 컬렉션에서 처음 20개의 레코드를 가져옵니다.



```
HTTP GET /devices?startIndex=0&size=20
HTTP GET /configurations?startIndex=0&size=20
```



#### 4.2. 장치의 모든 구성 탐색(Browse) [보조 컬렉션]

``` 
HTTP GET /devices/{id}/configurations
```

대부분 소규모 컬렉션이므로 여기에서 필터링이나 정렬을 활성화할 필요가 없습니다.



#### 4.3. 단일 장치 또는 구성 탐색(Browse)

장치 또는 구성의 전체 세부 정보를 얻으려면 GET단일 리소스 URI에 GET 작업을 사용합니다.

``` 
HTTP GET /devices/{id}
HTTP GET /configurations/{id}
```



#### 4.4. 장치에서 단일 구성 탐색(Browse)

```
HTTP GET /devices/{id}/configurations/{configId}
```

하위 리소스 representation은 기본 프레젠테이션과 같거나 하위 집합이 됩니다.



#### 4.5. 장치 또는 구성 만들기(Create)

CREATE는 멱등성(다회 적용해도 결과가 변하지 않는다) 작업이 아니며 HTTP 프로토콜 POST또한 멱등성이 아닙니다. 따라서 `POST`를 사용하십시오.

```
HTTP POST /devices
HTTP POST /configurations
```

서버가 결정할 책임이 있으므로 요청 페이로드에는 `id` 속성이 포함되지 않습니다. CREATE 요청에 대한 응답은 다음과 같습니다.



```
HTTP/1.1 201 Created
Content-Type: application/xml
Location: http://example.com/network-app/configurations/678678
 
<configuration id="678678">
  	<link rel="self" href="/configurations/678678" />
  	<content><![CDATA[...]]></content>
  	<status>active</status>
  	<link rel="raw configuration content" href="/configurations/678678/raw" />
</configuration>
```



#### 4.6. 장치 또는 구성 업데이트

업데이트 작업은 멱등 작업이고 HTTP PUT도 멱등 메서드입니다. 따라서 업데이트 작업에 PUT 메서드를 사용할 수 있습니다.

```
HTTP PUT /devices/{id}
HTTP PUT /configurations/{id}
```

PUT 응답은 다음과 같습니다.

```
HTTP/1.1 200 OK
Content-Type: application/xml
 
<configuration id="678678">
  	<link rel="self" href="/configurations/678678" />
  	<content><![CDATA[. updated content here .]]></content>
  	<status>active</status>
  	<link rel="raw configuration content" href="/configurations/678678/raw" />
</configuration>

```



#### 4.7. 장치 또는 구성 제거

제거(Removing)는 항상 `DELETE`작업입니다.

```
HTTP DELETE /devices/{id}
HTTP DELETE /configurations/{id}
```

리소스가 삭제를 위해 대기열에 있을 경우(비동기 작업) 성공한 응답은 `202(Accepted)`여야 하며, 리소스가 영구적으로 삭제된 경우(동기 작업)는 200(OK)/204 (No Content) 여야합니다.

비동기 작업의 경우 애플리케이션은 성공/실패 상태를 추적할 수 있는 작업 ID를 반환해야 합니다.

> *시스템에서 하위 리소스가 삭제될 때, 동작을 결정하려면 충분한 분석을 수행해야 합니다.일반적으로 이러한 요청에서 리소스를* **SOFT DELETE** *할 수 있습니다. 즉, 상태를 INACTIVE로 설정합니다.
>
> *이 접근 방식을 따르면 다른 위치에서도 해당 참조를 찾아 제거할 필요가 없습니다.*



#### 4.8. 장치에서 구성 적용 또는 제거

실제 애플리케이션에서는 장치에 구성을 적용해야 합니다. 또는 기본 컬렉션이 아닌 장치에서 구성을 제거할 수 있습니다. 이 경우 **멱등성** 특성 때문에 PUT 및 DELETE 메서드를 사용해야 합니다.

```
//Apply Configuration on a device
HTTP PUT /devices/{id}/configurations
 
//Remove Configuration on a device
HTTP DELETE /devices/{id}/configurations/{configId}
```



### **5. 추가 작업**

지금까지 우리는 객체 모델과 URI만을 설계하고 HTTP 메소드 또는 작업을 결정했습니다. 애플리케이션의 다른 측면에서도 작업해야 합니다.

1. 로깅
2. 보안
3. 검색 등



## **REST APIs with JAX-RS**

[REST API 디자인 튜토리얼](https://restfulapi.net/rest-api-design-tutorial-with-example/) 에서 네트워크 애플리케이션의 디자인 프로세스에 REST 원칙을 적용하는 방법을 배웠습니다. [이 게시물에서는 JAX-RS 2.0](https://github.com/jax-rs) (Java APIfor RESTful Services)을 사용하여 동일한 애플리케이션에 대한 REST API를 만드는 방법을 배웁니다.



### **1. JAX-RS 사양**

> JAX-RS는 REST 아키텍처 스타일의 원칙에 따라 설계 및 구현된 웹 애플리케이션을 개발, 공개 및 액세스하기 위한 이식 가능한 API를 제공합니다.



Java EE 6 릴리스는 **Java API for RESTful 웹 서비스(JAX-RS)** [ [JSR 311](https://javaee.github.io/jsr311/) ] 를 도입하여 RESTful 웹 서비스 API를 표준화하는 첫 번째 단계를 밟았습니다.

JAX-RS는 모든 Java EE 호환 애플리케이션 서버에서 REST API 코드의 이식성을 보장합니다. 최신 버전은 Java EE 7 플랫폼의 일부로 릴리스된 JAX-RS 2.0 [[JSR 339](https://jcp.org/en/jsr/detail?id=339)] 입니다.

JAX-RS는 일반 Java 객체에 Java 어노테이션을 적용하는 데 중점을 둡니다. JAX-RS에는 특정 URI 패턴 및 HTTP 작업을 Java 클래스의 특정 메소드에 바인딩하는 어노테이션이 있습니다. 또한 입력/출력 매개변수를 처리하는 데 도움이 되는 어노테이션이 있습니다.

JAX-RS가 사양이라고 이미 말했듯이, REST API 코드를 실행하려면 구현이 필요하다는 의미입니다. 현재 사용 가능한 유명한JAX-RS 구현은 다음과 같습니다.

- [Jersey](https://jersey.github.io/)

- [RESTEasy](https://resteasy.github.io/)

- [Apache CXF](https://cxf.apache.org/)

- [Restlet](https://restlet.com/)



### **2. JAX-RS 어노테이션**

JAX-RS 2.0에서 제공하는 몇 가지 필수 어노테이션을 살펴보겠습니다.



#### 2.1. @Path('resourcePath')

기본 `@Path` 어노테이션은 URI와 관련된 URI 경로를 일치시키는 데 사용됩니다. 리소스 클래스 또는 메서드에 지정할 수 있습니다.

```java
@Path("/configurations")
public class ConfigurationResource
{
  	@Path("/{id}")
  	@GET
  	public Response getConfigurationById(@PathParam("id") Integer id) {
  	  	//...
  	}

}
```



#### 2.2. @POST

`@POST` 어노테이션이 달린(annotated) 메서드는 일치하는 리소스 경로에 대한 `HTTP POST`요청을 처리합니다.

```java
@POST
@Consumes("application/xml")
public Response createConfiguration(Configuration config) {
  	//...
}
```



#### 2.3. @PUT

`@PUT`어노테이션이 달린 메서드(annotated)는 일치하는 리소스 경로에 대한 `HTTP PUT`요청을 처리합니다.

```java
@PUT
@Consumes("application/xml")
public Response updateConfiguration(@PathParam("id") Integer id, Configuration config){
  	//...
}
```



#### 2.4. @GET

`@GET` 어노테이션이 달린 메서드(annotated)는 일치하는 리소스 경로에 대한 `HTTP GET`요청을 처리합니다.

```java
@GET
@Path("/{id}")
public Response getConfigurationById(@PathParam("id") Integer id){
  	//...
}
```



#### 2.5. @DELETE

`@DELETE` 어노테이션이 달린 메서드는 일치하는 리소스 경로에 대한 `HTTP DELETE`요청을 처리합니다.

```java
@DELETE
@Path("/{id}")
public Response deleteConfiguration(@PathParam("id") Integer id){
  	//...
}
```



#### 2.6. @PathParam("매개변수 이름")

`@PathParam`은 URL에서 메소드 매개변수로 값(리소스 식별자)을 주입하는 데 사용됩니다.

```java
@DELETE
@Path("/{id}")
public Response deleteConfiguration(@PathParam("id") Integer id){
  	//...
}
```

위의 예에서 id from/`{id}`의 값은 `@PathParam("id")`의 Integer id 와 일치합니다.

예를 들어 URI `HTTP DELETE /configurations/22312`는 위의 메서드에 매핑 되고 `id`는 `22312`으로 채워집니다.



####  2.7. @Produces

`@Produces`어노테이션은 어노테이션이 있는 자원 메소드에 의해 전달되는 MIME 유형을 정의합니다. 클래스 수준과 메서드 수준에서 정의할 수 있습니다.

클래스 수준에서 정의된 경우 리소스 클래스의 모든 메서드는 메서드에서 재정의되지 않으면 동일한 MIME 형식을 반환합니다.



#### 2.8. @Consumes

`@Consumes` 어노테이션은 어노테이션이 있는 리소스 메서드가 사용하는 MIME 유형을 정의합니다.

```java
@POST
@Consumes("application/xml")
public Response createConfiguration(Configuration config) {
  	//...
}
```



#### 2.9. @Context

HATEOAS 링크를 빌드하기 위해서 JAX-RS 2.0은  `@Context` 어노테이션을 사용하여 얻을 수 있는 `UriInfo`클래스를 제공합니다.

```
@Context
UriInfo uriInfo;
```

> 기본적으로 JAX-RS 런타임은 명시적으로 구현되지 않은 경우 HEAD 및 OPTIONS 메소드를 자동으로 지원합니다.
>
> HEAD의 경우 런타임은 구현된 GET 메서드(있는 경우)를 호출하고 응답 엔티티(설정된 경우)를 무시합니다.
>
> OPTIONS 메서드는 'Allow' 헤더에서 지원되는 리소스 메서드 집합을 포함한 응답을 반환할 수 있습니다.



## **3. Maven 애플리케이션 생성**

[Maven](https://maven.apache.org/) 은 프로젝트 빌드, 보고 및 중앙 정보의 문서화를 포함한 소프트웨어 프로젝트 관리 및 이해 도구입니다. 예) pom.xml.

[Eclipse](https://www.eclipse.org/) 에서 maven을 사용하여 애플리케이션을 생성하려면 다음 단계를 따르세요.



- 다음에서 새 프로젝트 마법사 열기 `File > New > Maven Project`

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/Create-Maven-Application-Step-1.png" alt="Create Maven Application - Step 1" style="zoom: 80%;" />
</p>


- `Next` 버튼을 클릭합니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/Create-Maven-Application-Step-2.png" alt="Create Maven Application - Step 2" style="zoom:80%;" />
</p>


- `maven-archtype-webapp`을 선택합니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/Create-Maven-Application-Step-3.png" alt="Create Maven Application - Step 3" style="zoom:80%;" />
</p>


- 프로젝트 세부 정보를 입력하고 `Finish` 를 클릭합니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/Create-Maven-Application-Step-4.png" alt="Create Maven Application - Step 4" style="zoom:80%;" />
</p>




### **4. JAX-RS 종속성 포함**

JAX-RS 2.0은 [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html) 과 함께 번들로 제공 되므로[ JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)에 JDK 1.7 이상 버전이 있는 경우 JAX-RS를 별도로 포함할 필요는 없습니다. 그러나 위에 나열된 구현 중 하나를 포함해야 합니다.

이 예제에서는 [RESTEasy 3.1.2.Final](https://docs.jboss.org/resteasy/docs/3.1.2.Final/userguide/html_single/index.html) 을 사용하고 있습니다.



```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  	<modelVersion>4.0.0</modelVersion>
  	<groupId>net.restfulapi.app</groupId>
  	<artifactId>NetworkManagement</artifactId>
  	<packaging>war</packaging>
  	<version>0.0.1-SNAPSHOT</version>
  	<name>NetworkManagement</name>
  	<url>http://maven.apache.org</url>
  	<repositories>
  	  	<repository>
        	  	<id>jboss</id>
        	  	<name>jboss repo</name>
        	 <url>http://repository.jboss.org/nexus/content/groups/public/</url>
  	  	</repository>
  	</repositories>
  	<build>
  	  	<plugins>
  	  	<plugin>
        	  	<groupId>org.apache.maven.plugins</groupId>
              	  	<artifactId>maven-compiler-plugin</artifactId>
              	  	<version>3.2</version>
              	  	<configuration>
              	  	<source>1.8</source>
              	  	<target>1.8</target>
              	  	</configuration>
        	  	</plugin>
  	  	</plugins>
  	  	<finalName>NetworkManagement</finalName>
  	</build>
  	<dependencies>
  	  	<dependency>
        	  	<groupId>org.jboss.resteasy</groupId>
        	  	<artifactId>resteasy-jaxrs</artifactId>
        	  	<version>3.1.2.Final</version>
  	  	</dependency>
 
  	  	<dependency>
        	  	<groupId>org.jboss.resteasy</groupId>
        	  	<artifactId>resteasy-jaxb-provider</artifactId>
        	  	<version>3.1.2.Final</version>
  	  	</dependency>
  	  	
  	  	<dependency>
        	  	<groupId>org.jboss.resteasy</groupId>
        	  	<artifactId>resteasy-servlet-initializer</artifactId>
        	  	<version>3.1.2.Final</version>
  	  	</dependency>
 
  	  	<dependency>
        	  	<groupId>junit</groupId>
        	  	<artifactId>junit</artifactId>
        	  	<version>3.8.1</version>
        	  	<scope>test</scope>
  	  	</dependency>
  	</dependencies>
</project>
```

> `resteasy-servlet-initializer`아티팩트를 사용하면 Servlet 3.0 컨테이너에서 리소스 및 공급자를 자동으로 검색할 수 있습니다.



### **5. 리소스 표현 생성**

JAX-RS 에서는, 자원 표현은 [JAXB](https://www.oracle.com/technical-resources/articles/javase/jaxb.html) 어노테이션이 붙은 POJO 클래스입니다. 예) `@XmlRootElement`, `@XmlAttribute` and `@XmlElement` 등등.

이 예제에서는 두 가지 표현을 공개하고 있습니다. 자바 클래스를 만들어 봅시다.



#### 5.1. 구성의 컬렉션 리소스

```java
package net.restfulapi.app.rest.domain;
 
import java.util.List;
 
import javax.ws.rs.core.Link;
import javax.xml.bind.annotation.XmlAccessType;
import javax.xml.bind.annotation.XmlAccessorType;
import javax.xml.bind.annotation.XmlAttribute;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;
import javax.xml.bind.annotation.adapters.XmlJavaTypeAdapter;
 
@XmlRootElement(name = "configurations")
@XmlAccessorType(XmlAccessType.FIELD)
public class Configurations
{
  	@XmlAttribute
  	private Integer size;
 
  	@XmlJavaTypeAdapter(Link.JaxbAdapter.class)
  	@XmlElement
  	private Link link;
 
  	@XmlElement
  	private List<Configuration> configurations;
 
  	public Integer getSize() {
  	  	return size;
  	}
 
  	public void setSize(Integer size) {
  	this.size = size;
  	}
 
  	public Link getLink() {
  	  	return link;
  	}
 
  	public void setLink(Link link) {
  	  	this.link = link;
  	}
 
  	public List<Configuration> getConfigurations() {
  	  	return configurations;
  	}
 
  	public void setConfigurations
  	  	(List<Configuration> configurations) {
  	  	this.configurations = configurations;
  	}
}
```



#### 5.2. 단일 구성 리소스

```java
package net.restfulapi.app.rest.domain;
 
import javax.ws.rs.core.Link;
import javax.xml.bind.annotation.XmlAccessType;
import javax.xml.bind.annotation.XmlAccessorType;
import javax.xml.bind.annotation.XmlAttribute;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;
import javax.xml.bind.annotation.adapters.XmlJavaTypeAdapter;
 
import net.restfulapi.app.rest.domain.common.Status;
 
@XmlRootElement(name="configuration")
@XmlAccessorType(XmlAccessType.FIELD)
public class Configuration
{
  	@XmlAttribute
  	private Integer id;
 
  	@XmlJavaTypeAdapter(Link.JaxbAdapter.class)
  	@XmlElement
  	private Link link;
 
  	@XmlElement
  	private String content;
 
  	@XmlElement
  	private Status status;
 
  	public Link getLink() {
  	  	return link;
  	}
 
  	public void setLink(Link link) {
  	  	this.link = link;
  	}
 
  	public Integer getId() {
  	  	return id;
  	}
 
  	public void setId(Integer id) {
  	  	this.id = id;
  	}
 
  	public String getContent() {
  	  	return content;
  	}
 
  	public void setContent(String content) {
  	  	this.content = content;
  	}
 
  	public Status getStatus() {
  	  	return status;
  	}
 
  	public void setStatus(Status status) {
  	  	this.status = status;
  	}
}
```



#### 5.3. 메시지 리소스 [리소스 표현이 필요하지 않을 때 클라이언트에게 알리기]

```java
package net.restfulapi.app.rest.domain.common;
 
import javax.xml.bind.annotation.XmlRootElement;
 
@XmlRootElement(name = "message")
public class Message {
 
  	public Message() {
  	  	super();
  	}
 
  	public Message(String content) {
  	  	super();
  	  	this.content = content;
  	}
 
  	private String content;
 
  	public String getContent() {
  	  	return content;
  	}
 
  	public void setContent(String content) {
  	  	this.content = content;
  	}
}
```

또한 `ConfigurationDB` 클래스를 사용하여 DB 기능을 시뮬레이션 했습니다. Configuration리소스 컬렉션과 개별 Configuration 리소스에서 CRUD 작업의 정적 유틸리티 메서드를 노출합니다.

```java
package net.restfulapi.app.dao;
 
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicInteger;
 
import net.restfulapi.app.rest.domain.Configuration;
import net.restfulapi.app.rest.domain.common.Status;
 
public class ConfigurationDB
{
  	private static Map<Integer, Configuration> configurationDB
  	  	= new ConcurrentHashMap<Integer, Configuration>();
  	private static AtomicInteger idCounter = new AtomicInteger();
 
  	public static Integer createConfiguration(String content, Status status){
  	  	Configuration c = new Configuration();
        	c.setId(idCounter.incrementAndGet());
        	c.setContent(content);
        	c.setStatus(status);
        	configurationDB.put(c.getId(), c);
  	  	return c.getId();
  	}
 
  	public static Configuration getConfiguration(Integer id){
  	  	return configurationDB.get(id);
  	}
 
  	public static List<Configuration> getAllConfigurations(){
  	  	return new ArrayList<Configuration>(configurationDB.values());
  	}
 
  	public static Configuration removeConfiguration(Integer id){
  	  	return configurationDB.remove(id);
  	}
 
  	public static Configuration updateConfiguration(Integer id, Configuration c){
  	  	return configurationDB.put(id, c);
  	}
}
```



### **6. REST 리소스 생성**

두 번째 섹션에서 JAX-RS 주석에 대해 이미 배웠습니다. 이것을 REST 리소스에 적용하고 REST 리소스에 대한 작업에 HTTP 메서드를 매핑해 보겠습니다.

나는 그것을 설명하기 위해 각 방법 위에 따로 설명이 필요 없도록 코드 주석을 추가했습니다.



```java
package net.restfulapi.app.rest.service;
 
import java.util.List;
 
import javax.ws.rs.Consumes;
import javax.ws.rs.DELETE;
import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.PUT;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.core.Context;
import javax.ws.rs.core.Link;
import javax.ws.rs.core.Response;
import javax.ws.rs.core.UriBuilder;
import javax.ws.rs.core.UriInfo;
 
import net.restfulapi.app.dao.ConfigurationDB;
import net.restfulapi.app.rest.domain.Configuration;
import net.restfulapi.app.rest.domain.Configurations;
import net.restfulapi.app.rest.domain.common.Message;
import net.restfulapi.app.rest.domain.common.Status;
 
/**
* 이 REST 리소스에는 공통 경로 "/configurations"가 있습니다.
* 그리고 구성 컬렉션 리소스와 개별 컬렉션 리소스를 나타냅니다.
* 이 리소스의 기본 MIME 유형은 "application/XML"입니다.
* */
@Path("/configurations")
@Produces("application/xml")
public class ConfigurationResource
{
  	/**
  	* 이 두 가지 기본 구성으로 응용 프로그램을 초기화합니다.
  	* */
  	static {
  	  	ConfigurationDB.createConfiguration("Some Content", Status.ACTIVE);
  	  	ConfigurationDB.createConfiguration("Some More Content", Status.INACTIVE);
  	}
  	
  	/**
  	* uriInfo 를 사용해 현재의 context pass를 얻고, HATEOAS 링크를 구축한다
  	* */
  	@Context
  	UriInfo uriInfo;
 
  	/**
  	* 경로 "HTTP GET /configurations"에 매핑된 구성 컬렉션 리소스를 가져옵니다.
  	* */
  	@GET
  	public Configurations getConfigurations() {
 
  	  	List<Configuration> list = ConfigurationDB.getAllConfigurations();
 
  	  	Configurations configurations = new Configurations();
        	configurations.setConfigurations(list);
        	configurations.setSize(list.size());
 
  	  	// 기본 컬렉션 링크 설정
  	  	Link link = Link.fromUri(uriInfo.getPath()).rel("uri").build();
        	configurations.setLink(link);
 
  	  	// 구성 항목에 링크 설정
  	  	for(Configuration c: list){
  	  	Link lnk = Link.fromUri(uriInfo.getPath() + "/" + c.getId()).rel("self").build();
        	c.setLink(lnk);
  	  	}
  	  	return configurations;
  	}
 
  	/**
  	* 경로 'HTTP GET /configurations/{id}'에 매핑된 개별 구성 리소스를 가져옵니다.
  	* */
  	@GET
  	@Path("/{id}")
  	public Response getConfigurationById(@PathParam("id") Integer id){
  	  	Configuration config = ConfigurationDB.getConfiguration(id);
 
  	  	if(config == null) {
        	  	return Response.status(javax.ws.rs.core.Response.Status.NOT_FOUND)
                    	  	.build();
  	  	}
 
  	  	if(config != null){
        	  	UriBuilder builder = UriBuilder.fromResource(ConfigurationResource.class)
                           	  	.path(ConfigurationResource.class, "getConfigurationById");
        	  	Link link = Link.fromUri(builder.build(id))
                    	  	.rel("self")
                    	  	.build();
              	config.setLink(link);
  	  	}
 
  	  	return Response.status(javax.ws.rs.core.Response.Status.OK)
              	  	.entity(config)
              	  	.build();
  	}
 
  	/**
  	* 구성 컬렉션 리소스에 새 구성 리소스 만들기
  	 */
  	@POST
  	@Consumes("application/xml")
  	public Response createConfiguration(Configuration config){
  	  	if(config.getContent() == null) {
        	  	return Response.status(javax.ws.rs.core.Response.Status.BAD_REQUEST)
                    	  	.entity(new Message("Config content not found"))
                    	  	.build();
  	  	}
 
  	  	Integer id = ConfigurationDB.createConfiguration(config.getContent(), config.getStatus());
  	  	Link lnk = Link.fromUri(uriInfo.getPath() + "/" + id).rel("self")
                    	  	.build();
  	  	return Response.status(javax.ws.rs.core.Response.Status.CREATED)
              	  	.location(lnk.getUri())
              	  	.build();
  	}
 
  	/**
  	* 경로 "/configurations/{id}"의 "id"에서 기존 구성 리소스를 변경합니다.
  	* */
  	@PUT
  	@Path("/{id}")
  	@Consumes("application/xml")
  	public Response updateConfiguration(@PathParam("id") Integer id, Configuration config){
 
  	  	Configuration origConfig = ConfigurationDB.getConfiguration(id);
  	  	if(origConfig == null) {
        	  	return Response.status(javax.ws.rs.core.Response.Status.NOT_FOUND)
                    	  	.build();
  	  	}
 
  	  	if(config.getContent() == null) {
        	  	return Response.status(javax.ws.rs.core.Response.Status.BAD_REQUEST)
                    	  	.entity(new Message("Config content not found"))
                    	  	.build();
  	  	}
 
  	  	ConfigurationDB.updateConfiguration(id, config);
  	  	return Response.status(javax.ws.rs.core.Response.Status.OK)
                    	  	.entity(new Message("Config Updated Successfully"))
                    	  	.build();
  	}
 
  	/**
  	* 경로 "/configurations/{id}"의 "id"에서 구성 리소스를 삭제합니다.
  	* */
  	@DELETE
  	@Path("/{id}")
  	public Response deleteConfiguration(@PathParam("id") Integer id){
 
  	  	Configuration origConfig = ConfigurationDB.getConfiguration(id);
  	  	if(origConfig == null) {
        	  	return Response.status(javax.ws.rs.core.Response.Status.NOT_FOUND).build();
  	  	}
 
  	  	ConfigurationDB.removeConfiguration(id);
  	  	return Response.status(javax.ws.rs.core.Response.Status.OK).build();
  	}
}
```





### **7. 런타임에 리소스 등록**

JAX-RS REST 리소스를 서버 런타임에 등록하려면 `javax.ws.rs.core.Application`클래스를 확장하고 애플리케이션의 클래스 경로에 넣어야 합니다.

```java
package net.restfulapi.app.rest;
 
import java.util.HashSet;
import java.util.Set;
 
import javax.ws.rs.ApplicationPath;
import javax.ws.rs.core.Application;
 
import net.restfulapi.app.rest.service.ConfigurationResource;
 
@ApplicationPath("/network-management")
public class NetworkApplication extends Application {
 
  	private Set<Object> singletons = new HashSet<Object>();
  	private Set<Class<?>> empty = new HashSet<Class<?>>();
 
  	public NetworkApplication() {
        	singletons.add(new ConfigurationResource());
  	}
 
  	@Override
  	public Set<Class<?>> getClasses() {
  	  	return empty;
  	}
 
  	@Override
  	public Set<Object> getSingletons() {
  	  	return singletons;
  	}
}
```

여기 `@ApplicationPath`어노테이션은 이 클래스를 서블릿 3.0 컨테이너의 자동 스캔 프로세스에 대한 REST 애플리케이션으로 식별합니다. 이것은 REST 특정 구성을 전혀 사용하지 않고 `web.xml` 파일을 거의 비우는 데 도움이 됩니다.



```java
<web-app version="3.0" xmlns="http://java.sun.com/xml/ns/javaee"
  	  	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  	  	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
        	http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">
  	<display-name>Archetype Created Web Application</display-name>
</web-app>
```





### **8. Demo**

이 프로젝트를 빌드하여 웹 서버에 배포하고 서버를 시작합니다. 이제 브라우저 클라이언트에서 위의 URI를 호출하여 REST API를 테스트하십시오.

- HTTP GET

  http://localhost:8080/NetworkManagement/network-management/configurations

구성 컬렉션을 가져옵니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/HTTP-GET-Configuration-Collection-Resource.png" alt="HTTP GET - Configuration Collection Resource" style="zoom:80%;" />
</p>


HTTP GET

http://localhost:8080/NetworkManagement/network-management/configurations/1



개별 구성을 가져옵니다.
<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/HTTP-GET-Individual-Configuration-Resource.png" alt="HTTP GET - Individual Configuration Resource" style="zoom:80%;" />
</p>

HTTP POST

http://localhost:8080/NetworkManagement/network-management/configurations



새 구성 리소스를 만듭니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/HTTP-POST-Create-New-Resource.png" alt="HTTP POST - Create New Resource" style="zoom:80%;" />
</p>

HTTP PUT

http://localhost:8080/NetworkManagement/network-management/configurations/1



구성 리소스를 업데이트합니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/HTTP-PUT-Update-Individual-Configuration-Resource.png" alt="HTTP PUT - Update Individual Configuration Resource" style="zoom:80%;" />
</p>

HTTP DELETE

http://localhost:8080/NetworkManagement/network-management/configurations/1



구성 리소스를 제거합니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/HTTP-DELETE-Individual-Configuration-Resource.png" alt="HTTP DELETE - Individual Configuration Resource" style="zoom:80%;" />
</p>

주어진 다운로드 링크를 클릭하여 이 애플리케이션의 소스 코드를 다운로드하십시오.

[소스코드 다운로드](https://restfulapi.net/wp-content/downloads/NetworkManagement.zip)







# **4️⃣ FAQs**



## **PUT vs POST**



이것은 많은 사람들이 시스템을 설계할 때 HTTP PUT과 HTTP POST 방법 중 하나를 선택하는 데 어려워 합니다.

그러나 RFC 2616은 이 둘을 매우 명확하게 구분하지만, 복잡한 표현 때문에 헷갈릴 수 있습니다.

PUT 또는 POST 메서드를 언제 사용해야 하는지 알아봅시다.



### **1. PUT 과 POST 의 차이**



| PUT                                                          | POST                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| RFC-2616은 PUT method가 request body에 첨부된 entity를Request-URI를 호스팅하는 서버에 저장을 요구한다고 명시했습니다. <br /><br />Request-URI가 이미 존재하는 리소스를 참조하는 경우 업데이트 작업이 발생하고, 그렇지 않은 경우 Request-URI가 유효한 리소스 URI인 경우 생성 작업이 발생해야 합니다(클라이언트가 리소스 식별자를 결정할 수 있다고 가정했을 때).<br /><br />``PUT /questions/{question-id}`` | 이 POST방법은 요청에 첨부된 엔터티를 Request-Line의 Request-URI에 의해 식별된 리소스의 새 하위 항목으로 원본 서버에 수락하도록 요청하는 데 사용됩니다.<br/><br />이는 본질적으로 POSTrequest-URI가 컬렉션 URI여야 함을 의미합니다.<br /><br />`POST /questions` |
| PUT메서드는 [**멱등수**](https://restfulapi.net/idempotent-rest-apis/) 입니다. 따라서 요청을 여러 번 재시도하는 경우 단일 요청 호출과 동일해야 합니다. <br /><br />*노트: 멱등수란?연산을 여러번 적용하더라도 결과가 달라지지 않는 성질을 뜻합니다.<br /><br />네이버 메인 페이지를 새로고침 여러번 해도 똑같은 화면 나오는것도 일종의 idempotent라고 할 수 있습니다. | POST멱 등성이 **아닙니다**. 따라서 요청을 N번 재시도하면 서버에 생성된 N개의 서로 다른 URI가 있는 N개의 리소스가 생깁니다. |
| 이미 리소스 컬렉션의 일부인 **리소스 하나를** 수정하려는 경우 사용합니다. <br /><br />PUT은 리소스 전체를 대체합니다. 리소스의 일부를 업데이트 한다면 PATCH를 사용합니다. | **리소스 컬렉션 아래에 자식 리소스**를 추가하려는 경우 사용합니다. |
| PUT멱등원 이지만 **응답을 캐시** 하면 안 됩니다.             | 응답에 적절한 Cache-Control 또는 Expires 헤더 필드가 포함되어 있지 않으면 이 메서드에 대한 응답을 [**캐시**](https://restfulapi.net/caching/) **할 수 없습니다 .**<br/><br />그러나 303(기타 참조) 응답을 사용하여 사용자 에이전트가 캐시 가능한 리소스를 검색하도록 지시할 수 있습니다. |
| 일반적으로 현업에서는UPDATE 작업에 PUT을 사용**합니다**.     | 항상 **CREATE** 작업에는 POST를 사용합니다.                  |





### **2. PUT vs POST 예제**

네트워크 응용 프로그램을 설계한다고 가정해 보겠습니다. `POST`와 `PUT`을 언제 사용하는지 더 잘 이해하기 위해 몇 가지 URI와 그 목적을 나열해 보겠습니다

```
GET 	/device-management/devices       : 모든 장치( device) 가져오기
POST 	/device-management/devices       : 새 장치 만들기

GET 	/device-management/devices/{id}   : "id"로 식별되는 장치 정보를 가져온다.
PUT 	/device-management/devices/{id}   : "id"로 식별되는 장치 정보를 업데이트 한다.
DELETE	/device-management/devices/{id}   : “id”로 장치 삭제
```

다른 리소스에 대해서도 유사한 URI 디자인 방식을 따르세요.







## **N+1 Problem**



### **1. N+1 문제**

N+1 문제는 대부분 ORM의 맥락에서 논의됩니다. 이러한 종류의 문제에서 시스템은 부모 엔터티만 요청한 한 부모 엔터티의 N개의 자식을 로드해야 합니다.

기본적으로 ORM은 지연 로드가 비활성화된 상태로 구성되어 있으므로 상위 엔터티에 대해 하나의 쿼리를 실행하면 N개의 하위 엔터티에 대해 각각 하나씩 N개의 쿼리가 추가로 발생합니다.

이 N+1 문제는 종종 심각한 성능 병목 현상으로 간주되므로 응용 프로그램의 설계 수준에서 해결해야 합니다.



### **2. REST API의 N+1 문제**

대부분 직접적으로 관련되어 있지만 N+1 문제는 ORM에만 국한되지 않습니다. 이 문제는 REST API와 같은 웹 API의 컨텍스트와도 관련될 수 있습니다.

Web API의 경우 N+1 문제는 클라이언트 애플리케이션이 하나의 컬렉션 리소스 + N개의 자식 리소스를 가져오기 위해 서버를 N+1번 호출해야 하는 상황입니다.

이는 주로 컬렉션 리소스가 클라이언트 응용 프로그램이 사용자 인터페이스를 모두 구축하는 데 도움이 되는 자식 리소스에 대한 충분한 정보를 제공하지 않았기 때문입니다.

예를 들어 책 모음을 리소스로 반환하는 REST API가 있습니다.



```
<books uri="/books" size="100">
	<book uri="/books/1" id="1">
		<isbn>3434253561</isbn>
	</book>
	<book uri="/books/2" id="2">
		<isbn>3423423534</isbn>
	</book>
	<book uri="/books/3" id="3">
		<isbn>5352342344</isbn>
	</book>
	...
	...
</books>
```

여기 `/books`리소스 반환 목록에는 그것 `id`과 만 포함하는 정보가 포함되어 `isbn`있습니다.

`name`이 정보는 일반적으로 ISBN이 아닌 UI 로 책을 표시하려는 클라이언트 애플리케이션 UI를 빌드하는 데 충분하지 않습니다 .

경우에 따라 클라이언트는 저자 이름 및 발행 연도와 같은 다른 정보도 표시하고자 할 수 있습니다.

위의 시나리오에서 클라이언트 애플리케이션은 `/books/{id}`에서 각 개별 책 리소스에 대해 N번 더 많은 요청을 해야 합니다. 따라서 전체 클라이언트는 REST API를 N+1번 호출하게 됩니다.

위의 시나리오는 예시일 뿐입니다. 컬렉션 리소스의 정보가 부족하면 REST API에서 N+1 문제가 발생할 수 있다는 아이디어입니다.



### **3. N+1 문제를 푸는 방법**

위에서 이야기한 문제에 대해 좋은 점이 있다면 문제가 정확히 무엇인지 알고 있다는 것입니다. 그리고 이것은 해결 방법을 매우 쉽게 만듭니다.

> 컬렉션 리소스 내의 단일 리소스에 충분한 정보를 포함합니다.

API 소비자와 상의하거나, 유사한 애플리케이션 및 해당 사용자 인터페이스에 대한 시장 조사를 수행하거나, 단순히 고객 입장에 서야 할 수도 있습니다.

또한 클라이언트 요구 사항에 대한 이해가 향상됨에 따라 API를 점차 발전시킬 수 있습니다. 이것은[ API 버전 관리](https://restfulapi.net/versioning/) 를 사용하여 가능합니다 .





## **‘q’ Parameter**





### **1. API 클라이언트는 여러 형식을 지원합니다**.

REST API는 보다 구체적인 MIME 유형 과 같은 다양한 형식으로 리소스 표현을 반환할 수 있습니다.

클라이언트 애플리케이션 또는 브라우저는 HTTP Accept 헤더에서 지원되는 모든 MIME 유형을 요청할 수 있습니다.

기술적으로 Accept헤더는 쉼표로 구분된 값 형식(csv) 으로 여러 값을 가질 수 있습니다.

예를 들어 또는 형식을 Accept요청하는 헤더 는 다음과 같이 설정할 수 있습니다

```
Accept : text/html,application/xml
```



### **2. 'q' 매개변수**

때때로 클라이언트는 여러 유형의 MIME을 요청할 때 기본 설정을 지정하기를 원할 수 있습니다. 이 기본 설정을 지정하기 위해 q매개변수( 상대 품질 계수 )가 사용됩니다.

매개변수 의 값은 q는 0에서 1 사이일 수 있습니다 . 0은 가장 낮은 값(즉, 가장 선호도가 가장 낮음)이고 1이 가장 높은 값(즉, 가장 선호됨)입니다.

샘플 사용법은 다음과 같습니다.

```
Accept : text/html, application/xml;q=0.9, */*;q=0.8
```

위의 예에서 클라이언트는 먼저 text/html 형식의 응답을 선호할 것임을 서버에 표시합니다.

서버가 요청된 리소스에 대해 text/html 형식을 지원하지 않으면 형식을 보내야 `application/xml`합니다. 두 형식 모두 사용할 수 없는 경우 지원하는 형식( `*/*`)으로 응답을 보냅니다 .



### **3. HTTP 사양에 따름**

HTTP 사양에서 정의하는 방법은 다음과 같습니다.



> 각 미디어 범위 뒤에는 상대 품질 요소를 나타내는 "q" 매개변수로 시작하는 하나 이상의 수락 매개변수가 올 수 있습니다. 첫 번째 "q" 매개변수(가 있다면)는 media-range 매개변수를 accept-params에서 분리합니다.
>
> 품질 요소를 사용하면 사용자 또는 사용자 에이전트가 0에서 1 사이의 'q' 값 척도를 사용하여 해당 미디어 범위에 대한 상대적 선호도를 나타낼 수 있습니다.
>
> **기본값은 1 입니다.**



동일한 q값에 대해 두 개의 MIME 유형이 있는 경우 둘 중 더 구체적인 유형이 우선합니다.

예를 들어 둘 다 application/xml및 */*0.9의 기본 설정이 있는 application/xml경우 서버에서 서비스를 제공합니다.



> 헤더 필드가 없으면 Accept클라이언트가 모든 미디어 유형을 수락한다고 가정합니다.
>
> 헤더 필드가 있고 Accept서버가 결합된 필드 값에 따라 수용 가능한 응답을 보낼 수 없는 경우 서버는 `406 (not acceptable)` 응답을 보내야 합니다.







# **5️⃣ Resources**



## **What is an API?**

API(전체 형식은 Application Programming Interface)는 다른 소프트웨어가 비즈니스 작업을 수행하기 위해 상호 작용하기를 원할 경우 준수해야하는 약속한 계약(또는 사양)입니다.

API를 사용하면 두 개 이상의 소프트웨어 응용 프로그램이 잘 정의된 컴퓨팅 인터페이스를 통해 서로 통신할 수 있습니다.



### **1. 실생활에서의 API 예시**

모바일 앱은 API의 매우 친숙한 예 중 하나일 수 있습니다. 모바일 애플리케이션은 일반적으로 애플리케이션의 모든 UI 관련 부분을 포함합니다.

모바일 앱에 표시되는 모든 실시간 데이터는 API를 통해 가져옵니다. 모든 날씨 정보, 이메일, 게임 점수, 라이브 방송 및 더 많은 실시간 데이터는 서버에 배포된 API에서 가져옵니다.

마찬가지로 많은 최신 웹 사이트는 AJAX, 웹 소켓 등과 같은 기술을 사용하여 주문형 데이터(필요할 때 정확히 서버에서 가져옴)를 가져옵니다. 이러한 종류의 상호 작용은 API를 통해 발생합니다.

이 페이지에서 볼 수 있는 또 다른 예는 소셜 공유 버튼입니다. 클릭하면 작업 확인을 위한 팝업이 나타납니다.

확인하면 해당 게시물이 API를 통해 선택한 소셜 미디어 웹사이트와 공유됩니다. 소셜 공유 버튼은 해당 웹사이트에서 제공하는 API를 사용하여 인터넷의 모든 URL을 공유합니다.



### **2. API 사용 방법**

특정 비즈니스 목표 또는 작업을 제공하기 위해 API가 내부적으로 어떻게 작동하는지 알 필요는 없습니다. API와 상호 작용하는 방법만 알면 됩니다.

예를 들어, 모든 항공권 예약 애플리케이션은 항공사에서 공개한 API를 사용합니다. 고객이 이러한 애플리케이션을 사용하고 항공권을 예약할 때마다 애플리케이션은 승객 및 항공편 예약 정보를 API에 전달합니다.

예약 API는 데이터를 처리하고 고객을 위해 티켓을 예약하고 애플리케이션은 응답으로 예약 세부 정보와 함께 성공적인 응답을 받습니다.

예약 애플리케이션은 API가 내부적으로 어떻게 작동하는지 알 수 없으며 알 필요도 없습니다. 그들이 해야 할 일은 잘 정의된 형식의 예약 정보를 API에 전달하고 응답을 기다리는 것뿐입니다.

마찬가지로 모든 애플리케이션/모바일 앱은 API를 사용하거나 API를 다른 소프트웨어에 노출할 수 있습니다.



### **3. API의 목적은?**

소프트웨어 응용 프로그램은 조각으로 개발됩니다. 다른 위치에서 여러 번 소프트웨어를 작성하는 것을 방지하기 위해 재사용 가능한 구성 요소로 작성됩니다.

이러한 재사용 가능한 구성 요소는 API와 같은 인터페이스를 통해 다른 구성 요소 및 응용 프로그램에 노출됩니다.

API는 구성 요소를 표준, 재사용 가능, 사용자가 쉽게 이해하고 추상화하는 데 도움이 됩니다. 추상화는 최소한의 관련 정보만 다른 엔터티에 노출하고 작업을 수행하는 비즈니스 논리를 보호하는 데 도움이 됩니다.

재사용 가능한 API를 만드는 것은 사용자에게 도움이 될 뿐만 아니라 개발자의 삶도 편안하게 만듭니다. 정의된 API 범위는 구성 요소의 설계, 테스트, 구축, 관리 및 버전 관리에 도움이 됩니다.



### **4. API 개발 방법**



#### 4.1. API 사양

이미 말했듯이 API는 내부 작동 방식을 노출하지 않고 프로그래밍 인터페이스를 노출하는 재사용 가능한 추상 구성 요소입니다. 이 목표를 달성하기 위해 가장 먼저 해야 할 일은 표준적이고 깨끗하며 잘 문서화되고 사용하기 쉬운 API 사양입니다.

사양은 다음을 명확하게 해야 합니다.



- 수행할 매우 명확한 비즈니스 운영
- API URL 또는 인터페이스
- HTTP 프로토콜 및 메서드
- 요청 구조 및 개별 필드
- 응답 구조 및 개별 필드
- 해당되는 경우 요청의 필드에 유효한 값
- 데이터 파일링 및 정렬을 위한 모든 메커니즘
- 모든 인증/권한 정보
- 가능한 성공 및 오류 코드
- 그리고 기타 관련 정보



#### 4.2. API 보안

정보는 모든 애플리케이션, 특히 사용자의 비공개 개인 정보 (NPI 데이터) 에서 가장 중요합니다 . 이 정보를 보호하기 위해 API는 무단 액세스로부터 보호되어야 합니다.

보안 자격 증명이 있는 승인된 사용자만 API에 액세스할 수 있어야 합니다.



#### 4.3. 감사 로깅

공개적으로 노출된 API는 매일 수천 명의 사용자가 사용합니다. 사용자가 API를 어떻게 사용하거나 남용하는지 이해하려면 API 사용과 관련된 중요한 정보를 기록해야 합니다.

적절한 모니터링 시스템은 누군가가 API 인터페이스를 오용하거나 애플리케이션에 무단으로 입력하는 경우 경고를 트리거합니다.



#### 4.4. 성능

API 성능은 처음부터 사용할 수 있도록 하는 데 매우 중요합니다. 서버에서 요청이 처리되는 동안 사용자를 차단하려는 응용 프로그램은 없습니다. 특정 애플리케이션만 실시간으로 작업을 수행하는 위치를 차단할 것으로 예상됩니다 (예: 송금 및 청구서 지불)

성능이 좋지 않은 API는 소비자가 사용하지 않으므로 비즈니스에 손실이 발생합니다.

API 설계 및 모범 사례에 대한 당신의 생각을 알려주세요.





## **SOAP vs REST**

**SOAP ( Simple Object Access Protocol )는 XML과 스키마에 크게 의존 하는 매우 강력한 형식의 메시징 프레임워크 를 정의합니다.**

**REST ( REpresentational State Transfer )는 기존 기술과 널리 채택된 기술, 특히 HTTP를 사용하고 새로운 표준을 만들지 않는 아키텍처 스타일 입니다.** REST는 데이터를 XML, YAML 또는 기타 기계 판독 가능 형식으로 구조화할 수 있지만 일반적으로 JSON이 선호됩니다.

SOAP 및 REST – 둘 다 SOA 기반 애플리케이션 구축을 지원합니다. 사용할 항목의 선택은 비즈니스 및 아키텍처 관점에서 실제로 필요한 항목에 따라 크게 달라집니다.

특정 환경에서의 강점과 약점을 살펴보고 자신의 프로젝트 범위를 이해함으로써 가장 정보에 입각한 결정을 내릴 수 있습니다.



### **SOAP 및 REST API 비교**



#### 1. 기본 프로토콜

SOAP 자체는 SOAP 기반 API를 개발하기 위한 프로토콜(HTTP를 통한)입니다.

REST는 HTTP와 거의 동의어이지만 REST 사양에서는 이를 요구하지 않습니다.



#### 2. 데이터 형식

SOAP는 메시징 서비스를 제공하기 위해 전적으로 XML에 의존하며, 일부 경우에는 매우 복잡해질 수 있습니다(예: javascript를 통해 웹 서비스에 액세스).

REST는 CSV(쉼표로 구분된 값), JSON(JavaScript Object Notation) 및 RSS(Really Simple Syndication)의 데이터를 사용할 수 있습니다.

요점은 애플리케이션에 필요한 언어 내에서 구문 분석하기 쉬운 형식으로 필요한 출력을 얻을 수 있다는 것입니다.



#### 3. 상태유지(Statefulness)

RESTful 웹 서비스는 완전히 상태 비저장입니다. 대화 상태를 관리하는 것은 클라이언트 자체의 완전한 책임입니다. 서버는 이것을 도와주지 않습니다.

일반적으로 SOAP 웹 서비스는 상태 비저장이지만 서버에서 코드를 변경하여 SOAP API를 상태 저장으로 쉽게 만들 수 있습니다.



#### 4. 캐싱

REST는 HTTP 메서드를 통해 우수한 캐싱 인프라를 제공 GET하므로 응답 데이터를 캐시 가능 또는 캐시 불가능으로 표시할 수 있습니다. ETag 헤더 는 값을 계산할 수 있는 저렴한 방법이 있는 경우 캐싱을 구현하는 좋은 방법입니다.

SOAP는 HTTP를 전송 메커니즘으로 사용할 때 HTTP POST 요청을 통해 전송됩니다. HTTP POST는 멱등성이 아니므로 HTTP 수준에서 캐시할 수 없습니다. 따라서 SOAP 응답은 Response Caching Optimization Module 에 제공된 정보를 사용하여 캐시되어야 합니다.



#### 5. HTTP 동사 사용

REST는 주로 HTTP를 통해 사용 되며 다양한 CRUD 작업에 대해 HTTP GET, POST, PUT, DELETE 및 PATCH 메서드 를 사용합니다.

SOAP는 또한 HTTP 프로토콜에 대한 바인딩을 정의합니다. HTTP에 바인딩할 때 모든 SOAP 요청은 HTTP POST를 통해 전송됩니다.



#### 6. 보안

REST는 HTTP를 기반으로 합니다. HTTP 자체는 매우 안전하지 않은 프로토콜입니다. TLS를 통한 기본 인증 및 통신 암호화를 지원합니다. 추가 보안은 서버에서 추가로 구현되어야 합니다.

SOAP 보안은 WS-SECURITY 를 통해 잘 표준화 되어 있으며, 이는 기능이 매우 풍부하고 애플리케이션 코드에 구현하기 쉽습니다.



#### 7. 비동기 처리

리소스 생성/업데이트에 시간이 많이 소요되는 경우 비동기식 요청 처리가 필요할 수 있습니다. 이 경우 REST는 HTTP 응답 코드 202를 반환하고 작업 완료 상태가 자주 업데이트될 큐의 위치를 보낼 것을 제안합니다. REST는 JAX-RS 자체 에서 비동기 API에 대한 꽤 좋은 지원을 받았습니다.

애플리케이션에 보장된 수준의 안정성과 보안이 필요한 경우 SOAP 1.2는 이러한 유형의 작업을 보장하기 위해 추가 표준을 제공합니다. WSRM과 같은 것 – WS-Reliable Messaging.



### **요약**

전반적으로 REST는 이미 존재하는 웹을 활용하고 자유도가 제한되어 있기 때문에 개발하기가 더 간단합니다(선택 사항이 적을수록 더 간단해짐).

SOAP는 몇 가지 대안을 제공하며 개발하기가 약간 더 어렵지만 더 많은 대안과 작업 영역을 제공합니다.





## **HTTP Methods**

REST API를 사용하면 가능한 모든 CRUD(생성, 검색, 업데이트, 삭제) 작업이 있는 모든 종류의 웹 애플리케이션을 개발할 수 있습니다.

REST 지침 은 서버에 대한 특정 유형의 호출에 대해 특정 HTTP 메서드를 사용할 것을 제안합니다(기술적으로 이 지침을 위반할 수 있지만 권장하지 않음).

API가 수행하는 작업에 적합한 HTTP 메소드를 찾으려면 아래 정보를 사용하십시오.



### **1. HTTP GET**

**GET 요청 을 사용 하여 리소스 표현/정보만 검색 하고 어떤 식으로든 수정하지 마십시오.** GET 요청은 리소스의 상태를 변경하지 않으므로 안전한 방법 이라고 합니다.

또한 **GET API는 멱등해야 합니다.** 여러 개의 동일한 요청을 수행하면 다른 API(POST 또는 PUT)가 서버의 리소스 상태를 변경할 때까지 매번 동일한 결과가 생성되어야 합니다.

Request-URI가 데이터 생성 프로세스를 참조하는 경우 해당 텍스트가 프로세스의 출력이 아닌 한 프로세스의 소스 텍스트가 아니라 응답의 엔터티로 반환되어야 하는 생성된 데이터입니다.



#### 1.1. GET API 응답 코드

- 주어진 HTTP GET API의 경우 서버에서 리소스가 발견되면 200 (OK)일반적으로 XML 또는 JSON 콘텐츠인 응답 본문과 함께 HTTP 응답 코드를 반환해야 합니다(플랫폼 독립적인 특성으로 인해).

- 서버에서 리소스를 찾을 수 없는 경우 API는 HTTP 응답 코드 `404 (NOT FOUND)`를 반환해야 합니다.

- 마찬가지로 GET 요청 자체가 올바르게 구성되지 않은 것으로 확인되면 서버는 HTTP 응답 코드 `400 (BAD REQUEST)`를 반환합니다 .



#### 1.2. 예시 URI

```
HTTP GET http://www.appdomain.com/users
HTTP GET http://www.appdomain.com/users?size=20&page=5
HTTP GET http://www.appdomain.com/users/123
HTTP GET http://www.appdomain.com/users/123/address
```



### **2. HTTP POST**

POST API 를 사용하여 새 종속 리소스를 생성합니다 . 예를 들어 파일이 해당 파일을 포함하는 디렉토리에 종속되거나 행이 데이터베이스 테이블에 종속됩니다.

REST에 대해 엄격하게 이야기할 때 POST 메서드는 리소스 컬렉션에 새 리소스를 만드는 데 사용됩니다.
응답에 적절한 Cache-Control 또는 Expires 헤더 필드 가 포함되어 있지 않으면 이 메서드에 대한 응답을 캐시 할 수 없습니다.

POST는 안전하지도 멱등도 아니며 두 개의 동일한 POST 요청을 호출하면 동일한 정보(리소스 ID 제외)를 포함하는 두 개의 다른 리소스가 생성됩니다.



#### 2.1. POST API 응답 코드

- 이상적으로는 리소스가 원본 서버에서 생성된 경우 응답은 HTTP 응답 코드 201(Created) 이어야 하고 요청 상태를 설명하고 새 리소스를 참조하는 엔터티와 Location 헤더를 포함해야 합니다.
- 여러 번 POST 메서드로 수행된 작업으로 인해 URI로 식별할 수 있는 리소스가 생성되지 않을 수 있습니다. 이 경우 HTTP 응답 코드 `200 (OK)`또는 `204 (No Content)`적절한 응답 상태입니다.



#### 2.2. 예시 URI

```
HTTP POST http://www.appdomain.com/users
HTTP POST http://www.appdomain.com/users/123/accounts
```



### **3. HTTP PUT**

PUT API를 주로 사용 하여 기존 리소스를 업데이트합니다(리소스가 없으면 API에서 새 리소스 생성 여부를 결정할 수 있음).

요청이 캐시를 통과하고 Request-URI가 하나 이상의 현재 캐시된 엔터티를 식별하는 경우 해당 항목은 오래된 것으로 처리되어야 합니다(SHOULD). PUT 메소드에 대한 응답은 캐시할 수 없습니다.



#### 3.1. PUT API 응답 코드

- PUT API에 의해 새 리소스가 생성된 경우 원본 서버는 HTTP 응답 코드 `201 (Created)` 응답을 통해 사용자 에이전트에 알려야 합니다 .

- 기존 자원이 수정되면 `200 (OK)`또는 `204 (No Content)` 응답 코드를 보내서 요청이 성공적으로 완료되었음을 나타내야 합니다.



#### 3.2. 예시 URI

```
HTTP PUT http://www.appdomain.com/users/123
HTTP PUT http://www.appdomain.com/users/123/accounts/456
```



> POST API와 PUT API 의 [차이점은](https://restfulapi.net/rest-put-vs-post/) 요청 URI에서 확인할 수 있습니다. POST 요청은 리소스 컬렉션에 대해 수행되는 반면 PUT 요청은 단일 리소스에 대해 수행됩니다.



### **4. HTTP 삭제**

이름이 적용되면 DELETE API 는 리소스 (Request-URI로 식별됨)를 삭제합니다.

DELETE 작업은 멱등원 입니다. 리소스를 삭제하면 리소스 컬렉션에서 제거됩니다.

어떤 사람들은 DELETE 메소드를 멱등성이 아닌 것으로 만든다고 주장할 수 있습니다. 그것은 토론과 개인적인 의견의 문제입니다.

요청이 캐시를 통과하고 Request-URI가 하나 이상의 현재 캐시된 엔터티를 식별하는 경우 해당 항목은 오래된 것으로 처리되어야 합니다. 이 방법에 대한 응답은 캐시할 수 없습니다.



#### 4.1. DELETE API 응답 코드

- DELETE 요청의 성공적인 응답은 응답에 상태를 설명하는 엔터티가 포함된 경우 HTTP 응답 `code 200 (OK)`이어야 합니다.

- 작업이 대기열에 있는 경우 `202 (Accepted)`상태가 되어야 합니다.
- 상태는 작업이 수행되었지만 응답에 엔터티가 포함되지 않은 경우 `204 (No Content)`여야 합니다. 해당 리소스에서 DELETE API를 반복적으로 호출하면 결과가 변경되지 않습니다.

- 그러나 리소스에서 DELETE를 두 번째 호출하면 이미 제거되었기 때문에 `404(NOT FOUND)`가 반환됩니다.



#### 4.2. 예시

```
HTTP DELETE http://www.appdomain.com/users/123
HTTP DELETE http://www.appdomain.com/users/123/accounts/456
```





### **5. HTTP 패치**

HTTP PATCH 요청은 리소스를 부분적으로 업데이트하기 위한 것 입니다.

PUT 요청이 표시되면 리소스 엔터티도 수정합니다. 따라서 좀 더 정확하게 하자면 PATCH 방법은 기존 리소스를 부분적으로 업데이트하는 올바른 선택이며 리소스 전체를 교체하는 경우에만 PUT을 사용해야 합니다.

애플리케이션에서 PATCH API를 사용하기로 결정한 경우 몇 가지 문제가 있음을 유의하십시오.

브라우저, 서버 및 웹 애플리케이션 프레임워크에서 PATCH에 대한 지원은 보편적이지 않습니다. IE8, PHP, Tomcat, Django 및 기타 많은 소프트웨어에 대한 지원이 누락되었거나 중단되었습니다.

PATCH 요청의 요청 페이로드는 PUT 요청과 마찬가지로 간단하지 않습니다.



예시

```
HTTP GET /users/1
```

아래 응답을 생성합니다.

```
{ "id": 1, "username": "admin", "email": "email@example.org"}
```

이메일 업데이트를 위한 샘플 패치 요청은 다음과 같습니다.

```
HTTP PATCH /users/1
[{ "op": "replace", "path": "/email", "value": "new.email@example.org" }]
```

HTTP 사양에 따라 다음과 같은 가능한 작업이 있을 수 있습니다.

```
[
 { "op": "test",  "path": "/a/b/c",  "value": "foo"  },
 { "op": "remove",  "path": "/a/b/c"  },
 { "op": "add",  "path": "/a/b/c",  "value": [ "foo", "bar" ] },
 { "op": "replace", "path": "/a/b/c",  "value": 42 },
 { "op": "move",  "from": "/a/b/c",  "path": "/a/b/d" },
 { "op": "copy", "from": "/a/b/d",  "path": "/a/b/e" }
 ]
```

>PATCH 방법은 POST 또는 PUT 방법을 대체하지 않습니다. 전체 리소스를 교체하는 대신 델타(diff)를 적용합니다.



### **6. HTTP 메소드 요약**

아래 표는 위에서 논의한 HTTP 메소드의 사용을 요약한 것입니다.



| **HTTP 메소드** | **CRUD**           | **컬렉션 리소스(예: /users)**                                | **단일 리소스(예: /users/123)**                              |
| --------------- | ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| POST            | 만들다             | 201(Created), 새 ID를 포함하는 /users/{id}에 대한 링크가 있는 '위치' 헤더 | 단일 리소스에서 POST를 사용하지 마십시오.                    |
| GET             | 읽다               | 200(OK), 사용자 목록입니다. 페이지 매김, 정렬 및 필터링을 사용하여 큰 목록 탐색 | 200(OK), 단일 사용자. 404(찾을 수 없음), ID가 없거나 유효하지 않은 경우 |
| PUT             | 업데이트/교체      | 405(Method not allowed), 전체 리소스 컬렉션의 모든 리소스를 업데이트하지 않으려는 경우 | 200(Ok) 또는 204(No Content). ID가 없거나 유효하지 않은 경우 404(Not Founded) 사용 |
| PATCH           | 부분 업데이트/수정 | 405(Method not allowed), 컬렉션 자체를 수정하지 않으려는 경우 | 200(Ok) 또는 204(No Content). ID가 없거나 유효하지 않은 경우 404(Not Founded) 사용 |
| DELETE          | 삭제               | 405(Method not allowed), 전체 컬렉션을 삭제하지 않으려는 경우 — **주의해서 사용** | 200(Ok) 또는 404(Not Founded) ID가 없거나 유효하지 않은 경우 |



### **7. 용어집**



#### 7.1. 안전한 방법

정의된 의미 체계가 기본적으로 읽기 전용인 경우 요청 메서드는 *안전한 것으로 간주됩니다.* 클라이언트는 대상 리소스에 안전한 방법을 적용한 결과 원본 서버의 상태 변경을 요청하지 않으며 예상하지도 않습니다.

GET, HEAD, OPTIONS 및 TRACE 메서드는 안전한 메서드로 간주됩니다 . HTTP 사양에 따라 GET 및 HEAD 메서드는 리소스 표시 검색에만 사용해야 하며 서버의 리소스를 업데이트/삭제하지 않습니다.안전한 방법과 안전하지 않은 방법을 구분 하는 목적은 자동화된 검색 프로세스(스파이더)와 캐시 성능 최적화(프리페칭)가 피해를 입을 염려 없이 작동하도록 하는 것입니다.

*안전한 방법을 사용하면 사용자 에이전트가 POST, PUT 및 DELETE* 와 같은 다른 방법을 고유한 방식으로 나타낼 수 있으므로 사용자가 안전하지 않을 수 있는 작업이 요청되고 있다는 사실을 알 수 있으며 리소스를 업데이트/삭제할 수 있습니다. 서버 등을 주의해서 사용해야 합니다.



#### 7.2. 멱등수 메서드

멱등성이라는 용어는 한 번 또는 여러 번 실행되는 경우 동일한 결과를 생성하는 작업 을 설명하기 위해 보다 포괄적으로 사용됩니다.

HTTP 사양에서 PUT, DELETE 및 안전한 메서드( GET, HEAD, OPTIONS, TRACE )는 멱등 메서드 입니다.멱등은 의도하지 않은 효과를 일으키지 않고 필요한 만큼 자주 작업을 반복하거나 재시도할 수 있음을 의미하므로 많은 상황에서 편리한 속성입니다.

멱등성이 없는 작업의 경우 알고리즘은 작업이 이미 수행되었는지 여부를 추적해야 할 수 있습니다.*안전한 메서드* 의 정의와 마찬가지로 멱등 속성은 사용자가 요청한 경우에만 적용됩니다.

서버는 각 요청을 개별적으로 기록하거나 개정 제어 기록을 유지할 수 있습니다.





## **Richardson Maturity Model**



### **1. Richardson Maturity Model 이란?**

Leonard Richardson은 100가지 다른 웹 서비스 디자인을 분석하고 이러한 디자인을 4가지 범주로 나누었습니다. 이러한 범주는 웹 서비스가 얼마나 [REST를 준수하는지](https://restfulapi.net/rest-architectural-constraints/) 에 따라 다릅니다.

REST 서비스의 이 분할 모델은 성숙도 수준을 식별하기 위해 Richardson Maturity Model 이라고 합니다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221115144522324.png" alt="image-20221115144522324" style="zoom:80%;" />
</p>

Richardson 은 서비스의 성숙도를 결정하기 위해 세 가지 주요 요소를 사용 했습니다.

1. [URI](https://restfulapi.net/resource-naming/)
2. [HTTP 메소드](https://restfulapi.net/http-methods/)
3. [HATEOAS(하이퍼미디어)](https://restfulapi.net/hateoas/)

서비스가 이러한 요소를 많이 사용할수록 더 성숙한 것으로 간주됩니다.



### **2. 성숙도**

그의 분석에서 Richardson은 다음과 같이 총 4개의 성숙도 수준을 설명했습니다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/Richardson-Maturity-Model.jpg" alt="Richardson Maturity Model" style="zoom: 67%;" />
</p>


#### 2.1. Level Zero

> 성숙도 레벨 0은 URI, HTTP 메소드 및 HATEOAS 기능을 사용하지 않습니다.

성숙도가 0인 서비스는 단일 URI를 가지며 단일 HTTP 메소드(일반적으로 POST)를 사용합니다.

예를 들어, 대부분의 SOAP 웹 서비스는 단일 URI를 사용하여 끝점을 식별하고 HTTP POST를 사용하여 SOAP 기반 페이로드를 전송하여 나머지 HTTP 동사를 효과적으로 무시합니다.

마찬가지로 XML-RPC 기반 서비스는 데이터를 POX( *Plain Old XML )로 보냅니다.*

이것은 단일 POST 메서드 끝점으로 SOA 응용 프로그램을 구축하고 XML을 사용하여 클라이언트와 서버 간에 통신하는 가장 기본적인 방법입니다.



#### 2.2. Level One

> 성숙도 1단계 는 URI 를 사용하지만 HTTP 메소드 및 HATEOAS는 사용하지 않습니다.

성숙도 수준 1의 서비스는 많은 URI를 사용하지만 단일 HTTP 동사(일반적으로 HTTP POST)만 사용합니다.

이러한 서비스는 시스템에서 사용 가능한 각 리소스에 고유한 URI를 제공합니다. 고유한 URI는 하나의 고유한 리소스를 개별적으로 식별하므로 이러한 서비스가 수준 0보다 우수합니다.



#### 2.3. Level Two

> 레벨 2는 URI와 HTTP 메소드 를 사용하지만 HATEOAS는 사용하지 않습니다.

레벨 2 서비스는 일반적으로 주소 지정 가능한 리소스와 같은 수많은 URI를 호스트합니다.

이러한 서비스는 노출된 각 리소스에서 CRUD(Create, Read, Update 및 Delete) 서비스와 같은 여러 HTTP 동사를 지원합니다. 여기에서 일반적으로 비즈니스 엔터티를 나타내는 리소스 상태는 네트워크를 통해 조작할 수 있습니다.

레벨 2 서비스의 디자이너는 사람들이 일반적으로 제공된 문서를 읽고 API를 마스터하는 데 어느 정도 노력을 기울일 것으로 기대합니다.

성숙도 수준 2는 REST 원칙의 가장 인기 있는 사용 사례로, HTTP 요청 방법을 기반으로 다른 동사를 사용하는 것을 지지하는 반면 시스템에는 여러 리소스가 있을 수 있습니다.



#### 2.4. Level Three

> 성숙도 3단계는 URI와 HTTP, HATEOAS 3가지 모두를 사용합니다.

레벨 3은 리처드슨 모델의 가장 성숙한 레벨로, 쉽게 검색할 수 있습니다. 이 수준을 사용하면 HATEOAS를 사용하여 응답이 자기 설명적(self-descriptive)이 되기 쉽습니다.

레벨 3 서비스는 리소스 추적을 통해 서비스 소비자를 안내하여 결과적으로 애플리케이션 상태 전환을 유발합니다.









## **HTTP Response Codes**

REST API는 HTTP 응답 메시지의 일부인 상태 표시줄(Status-Line) 을 사용하여 클라이언트에게 요청의 중요한 결과를 알려줍니다. RFC 2616은 아래와 같이 상태 표시줄 구문을 정의합니다.



> Status-Line = HTTP-Version SP Status-Code SP Reason-Phrase CRLF



HTTP는 클라이언트 요청의 결과를 전달하는 데 사용할 수 있는 이러한 표준 상태 코드를 정의합니다. 상태 코드는 5가지 범주로 나뉩니다.



- 1xx: 정보 - 전송 프로토콜 수준 정보를 전달합니다.
- 2xx: 성공 – 클라이언트의 요청이 성공적으로 수락되었음을 나타냅니다.
- 3xx: 리디렉션 – 클라이언트가 요청을 완료하기 위해 몇 가지 추가 작업을 수행해야 함을 나타냅니다.
- 4xx: 클라이언트 오류 – 이 범주의 오류 상태 코드는 클라이언트를 가리킵니다.
- 5xx: 서버 오류 – 서버는 이러한 오류 상태 코드를 담당합니다.



### **1xx Status Codes [Informational]**

| **상태코드**                | **설명**                                                     |
| --------------------------- | ------------------------------------------------------------ |
| **100 Continue**            | 임시 응답입니다. 요청의 초기 부분이 수신되었으며 아직 서버에서 거부되지 않았음을 클라이언트에 나타냅니다. 클라이언트는 요청의 나머지 부분을 계속 전송하거나 요청이 이미 완료된 경우 이 응답을 무시해야 합니다. 서버는 요청이 완료된 후 최종 응답을 보내야 합니다. |
| **101 Switching Protocol**  | 클라이언트의 업그레이드 요청 헤더에 대한 응답으로 전송되며 서버가 전환하는 프로토콜을 나타냅니다. |
| **102 Processing (WebDAV)** | 서버가 요청을 수신하여 처리 중이지만 아직 사용할 수 있는 응답이 없음을 나타냅니다. |
| **103 Early Hints**         | 주로 Link헤더와 함께 사용하기 위한 것입니다. 서버가 최종 응답을 준비하는 동안 사용자 에이전트가 리소스를 미리 로드하기 시작하도록 제안합니다. |





### **2xx Status Codes [Success]**

| **상태코드**                          | **설명**                                                     |
| ------------------------------------- | ------------------------------------------------------------ |
| **200 OK**                            | 요청이 성공했음을 나타냅니다.                                |
| **201 Created**                       | 요청이 성공했으며 결과적으로 새 리소스가 생성되었음을 나타냅니다. |
| **202 Accepted**                      | 요청이 수신되었지만 아직 완료되지 않았음을 나타냅니다. 일반적으로 로그 실행 요청 및 일괄 처리에 사용됩니다. |
| **203 Non-Authoritative Information** | entity-header에서 반환된 메타 정보가 원본 서버에서 사용할 수 있는 최종 집합이 아니라 로컬 또는 타사 복사본에서 수집됨을 나타냅니다. 제시된 세트는 원래 버전의 하위 세트 또는 상위 세트일 수 있습니다. |
| **204 No Content**                    | 서버가 요청을 수행했지만 응답 본문을 반환할 필요가 없습니다. 서버는 업데이트된 메타 정보를 반환할 수 있습니다. |
| **205 Reset Content**                 | 이 요청을 보낸 문서를 재설정할 클라이언트를 나타냅니다.      |
| **206 Partial Content**               | 리소스의 일부만 요청하기 위해 클라이언트에서 Range 헤더를 보낼 때 사용됩니다. |
| **207 Multi-Status (WebDAV)**         | 여러 작업이 발생했으며 각 작업의 상태가 응답 본문에서 찾을 수 있음을 클라이언트에 표시합니다. |
| **208 Already Reported (WebDAV)**     | 클라이언트가 서버에 동일한 리소스(동일한 바인딩 사용)가 이전에 언급되었음을 알릴 수 있습니다. 상태 표시줄에 진정한 HTTP 응답 코드로 나타나지 않으며 본문에만 나타납니다. |
| **226 IM Used**                       | 서버는 리소스에 대한 GET 요청을 수행했으며 응답은 현재 인스턴스에 적용된 하나 이상의 인스턴스 조작 결과를 나타냅니다. |



### **3xx Status Codes [Redirection]**

| **상태코드**                              | **설명**                                                     |
| ----------------------------------------- | ------------------------------------------------------------ |
| **300 Multiple Choices**                  | 요청에 가능한 응답이 두 개 이상 있습니다. 사용자 에이전트 또는 사용자는 그 중 하나를 선택해야 합니다. |
| **301 Moved Permanently**                 | **301 Moved Permanently**요청한 리소스의 URL이 영구적으로 변경되었습니다. 새 URL은 응답의 Location 헤더 필드에 의해 제공됩니다. 이 응답은 달리 표시되지 않는 한 캐시할 수 있습니다. |
| **302 Found**                             | **302 Found**요청한 리소스의 URL이 일시적으로 변경되었습니다. 새 URL은 응답의 위치 필드에 의해 제공됩니다. 이 응답은 Cache-Control 또는 Expires 헤더 필드로 표시되는 경우에만 캐시할 수 있습니다. |
| **303 See Other**                         | 응답은 다른 URI에서 찾을 수 있으며 해당 리소스에서 GET 메서드를 사용하여 검색해야 합니다(SHOULD). |
| **304 Not Modified**                      | 클라이언트가 응답이 수정되지 않았으므로 클라이언트가 계속해서 동일한 캐시된 버전의 응답을 사용할 수 있음을 나타냅니다. |
| **305 Use Proxy (Deprecated)**            | 프록시에서 요청된 응답에 액세스해야 함을 나타냅니다.         |
| **306 (Unused)**                          | 예약된 상태 코드이며 더 이상 사용되지 않습니다.              |
| **307 Temporary Redirect**                | 클라이언트가 이전 요청에 사용된 것과 동일한 방법으로 다른 URI에서 요청된 리소스를 가져오도록 지정합니다. 이전 요청에서 사용된 것과 동일한 HTTP 메소드가 사용된다는 점을 제외하고는 302 Found와 유사합니다. |
| **308 Permanent Redirect (experimental)** | 리소스가 이제 Location 헤더로 지정된 다른 URI에 영구적으로 있음을 나타냅니다. 301 Moved Permanently와 비슷하지만 이전 요청에서 사용된 것과 동일한 HTTP 메서드가 사용된다는 점만 제외하면 됩니다. |



### **4xx Status Codes [Client Error]**

| **상태코드**                                             | **설명**                                                     |
| -------------------------------------------------------- | ------------------------------------------------------------ |
| **400 Bad Request**                                      | 잘못된 구문으로 인해 서버에서 요청을 이해할 수 없습니다. 클라이언트는 수정 없이 요청을 반복해서는 안 됩니다. |
| **401 Unauthorized**                                     | 요청에 사용자 인증 정보가 필요함을 나타냅니다. 클라이언트는 적절한 Authorization 헤더 필드를 사용하여 요청을 반복할 수 있습니다. |
| **402 Payment Required (Experimental)**                  | 향후 사용을 위해 예약되어 있습니다. 디지털 결제 시스템에서 사용하기 위한 것입니다. |
| **403 Forbidden**                                        | 승인되지 않은 요청입니다. 클라이언트는 콘텐츠에 대한 액세스 권한이 없습니다. 401과 달리 클라이언트의 ID는 서버에 알려져 있습니다. |
| **404 Not Found**                                        | 서버가 요청한 리소스를 찾을 수 없습니다.                     |
| **405 Method Not Allowed**                               | 요청 HTTP 메서드는 서버에 알려져 있지만 비활성화되어 해당 리소스에 사용할 수 없습니다. |
| **406 Not Acceptable**                                   | 서버는 요청에서 보낸 Accept 헤더에서 사용자 에이전트가 제공한 기준을 준수하는 콘텐츠를 찾지 못합니다. |
| **407 Proxy Authentication Required**                    | 클라이언트가 먼저 프록시로 자신을 인증해야 함을 나타냅니다.  |
| **408 Request Timeout**                                  | 서버가 서버의 할당된 시간 초과 기간 내에 클라이언트로부터 완전한 요청을 받지 못했음을 나타냅니다. |
| **409 Conflict**                                         | 리소스의 현재 상태와 충돌하여 요청을 완료할 수 없습니다.     |
| **410 Gone**                                             | 요청한 리소스를 더 이상 서버에서 사용할 수 없습니다.         |
| **411 Length Required**                                  | 서버는 정의된 Content-Length 없이 요청 수락을 거부합니다. 클라이언트는 유효한 Content-Length 헤더 필드를 추가하는 경우 요청을 반복할 수 있습니다. |
| **412 Precondition Failed**                              | 클라이언트가 헤더에 서버가 충족하지 않는 전제 조건을 표시했습니다. |
| **413 Request Entity Too Large**                         | 요청 엔터티가 서버에서 정의한 제한보다 큽니다.               |
| **414 Request-URI Too Long**                             | 클라이언트가 요청한 URI가 서버가 해석할 수 있는 것보다 깁니다. |
| **415 Unsupported Media Type**                           | 요청의 Content-type에 있는 media-type은 서버에서 지원하지 않습니다. |
| **416 Requested Range Not Satisfiable**                  | 요청의 Range 헤더 필드에 지정된 범위를 충족할 수 없습니다.   |
| **417 Expectation Failed**                               | Expect 요청 헤더 필드에 표시된 예상은 서버에서 충족될 수 없습니다. |
| **418 I’m a teapot (RFC 2324)**                          | 4월의 엉뚱한 농담(만우절)으로 정의되었으며 실제 HTTP 서버에서는 구현되지 않을 것으로 예상됩니다. (RFC 2324) |
| **420 Enhance Your Calm (Twitter)**                      | 클라이언트가 속도 제한을 받고 있을 때 Twitter Search and Trends API에 의해 반환됩니다. |
| **422 Unprocessable Entity (WebDAV)**                    | 서버는 요청 엔터티의 콘텐츠 유형과 구문을 이해하지만 여전히 서버는 어떤 이유로 요청을 처리할 수 없습니다. |
| **423 Locked (WebDAV)**                                  | 액세스 중인 리소스가 잠겨 있습니다.                          |
| **424 Failed Dependency (WebDAV)**                       | 이전 요청의 실패로 인해 요청이 실패했습니다.                 |
| **425 Too Early (WebDAV)**                               | 서버가 재생될 수 있는 요청을 처리하는 위험을 감수하지 않음을 나타냅니다. |
| **426 Upgrade Required**                                 | 서버가 요청 수행을 거부합니다. 서버는 클라이언트가 다른 프로토콜로 업그레이드한 후 요청을 처리합니다. |
| **428 Precondition Required**                            | 원본 서버는 조건부 요청을 요구합니다.                        |
| **429 Too Many Requests**                                | 사용자가 주어진 시간 동안 너무 많은 요청을 보냈습니다("속도 제한"). |
| **431 Request Header Fields Too Large**                  | 헤더 필드가 너무 커서 서버가 요청을 처리하지 않습니다.       |
| **444 No Response (Nginx)**                              | Nginx 서버는 클라이언트에 정보를 반환하지 않고 연결을 닫습니다. |
| **449 Retry With (Microsoft)**                           | 적절한 작업을 수행한 후 요청을 다시 시도해야 합니다.         |
| **450 Blocked by Windows Parental Controls (Microsoft)** | Windows 자녀 보호가 켜져 있고 지정된 웹 페이지에 대한 액세스를 차단하고 있습니다. |
| **451 Unavailable For Legal Reasons**                    | 사용자 에이전트가 법적으로 제공할 수 없는 리소스를 요청했습니다. |
| **499 Client Closed Request (Nginx)**                    | HTTP 서버가 요청을 처리하는 동안 클라이언트에 의해 연결이 닫히므로 서버가 HTTP 헤더를 다시 보낼 수 없습니다. |



### **5xx Status Codes [Server Error]**

| **상태코드**                                      | **설명**                                                     |
| ------------------------------------------------- | ------------------------------------------------------------ |
| **500 Internal Server Error**                     | 서버에서 요청을 이행하지 못하게 하는 예기치 않은 조건이 발생했습니다. |
| **501 Not Implemented**                           | HTTP 메서드는 서버에서 지원하지 않으며 처리할 수 없습니다.   |
| **502 Bad Gateway**                               | 요청을 처리하는 데 필요한 응답을 얻기 위해 게이트웨이로 작업하는 동안 서버가 잘못된 응답을 받았습니다. |
| **503 Service Unavailable**                       | 서버가 요청을 처리할 준비가 되지 않았습니다.                 |
| **504 Gateway Timeout**                           | 서버가 게이트웨이 역할을 하고 있어 요청에 대한 응답을 제때 받을 수 없습니다. |
| **505 HTTP Version Not Supported (Experimental)** | 요청에 사용된 HTTP 버전은 서버에서 지원하지 않습니다.        |
| **506 Variant Also Negotiates (Experimental)**    | 서버에 내부 구성 오류가 있음을 나타냅니다. 선택한 변형 리소스가 명백한 콘텐츠 협상 자체에 참여하도록 구성되어 있으므로 협상 프로세스에서 적절한 끝점이 아닙니다. |
| **507 Insufficient Storage (WebDAV)**             | 서버가 요청을 성공적으로 완료하는 데 필요한 표현을 저장할 수 없기 때문에 리소스에서 메서드를 수행할 수 없습니다. |
| **508 Loop Detected (WebDAV)**                    | 서버가 요청을 처리하는 동안 무한 루프를 감지했습니다.        |
| **510 Not Extended**                              | 서버가 이를 수행하려면 요청에 대한 추가 확장이 필요합니다.   |
| **511 Network Authentication Required**           | 클라이언트가 네트워크 액세스 권한을 얻기 위해 인증해야 함을 나타냅니다. |

---



### **200 (OK)**

**HTTP 상태 200** (OK) 상태 코드는 요청 이 서버에서 **성공적으로 처리 되었음을 나타냅니다.** 응답 페이로드는 요청에 대해 선택된 HTTP 메서드에 따라 다릅니다.



#### 1. 응답 엔티티

| **HTTP 방식** | 응답 페이로드                                                |
| ------------- | ------------------------------------------------------------ |
| **GET**       | **요청된 리소스에 해당** 하는 엔터티 가 응답으로 전송됩니다. |
| **HEAD**      | 응답에는 **HTTP 헤더 필드만 있고 페이로드** 는 응답으로 전송되지 않습니다. |
| **POST**      | 응답에는 일반적으로 진행 **상태 표현 또는** 요청에서 수행된 작업의 결과에 대한 정보가 포함됩니다. |
| **PUT**       | **요청의 진행 상태 표시가** 응답으로 전송됩니다.             |
| **DELETE**    | **요청의 진행 상태 표시가** 응답으로 전송됩니다.             |
| **OPTIONS**   | **[Allow](https://tools.ietf.org/html/rfc7231#section-7.4.1)** 헤더 를 사용 **하여 리소스와 연결된 유효한 요청 방법** 목록입니다 . <br />예:<br />Allow: HEAD, GET, OPTIONS<br/>Content-Length: 18<br/>Content-Type: text/plain;charset=UTF-8<br/>Date: Thu, 06 Apr 2017 06:43:59 GMT<br/>Server: Apache-Coyote/1.1 |
| **TRACE**     | 최종 서버에서 받은 요청 메시지의 표현입니다.                 |



#### 2. 중요한 사실

- 200 응답 에는 **항상 페이로드** 가 있지만 원본 서버는 길이가 0인 페이로드 본문 또는 빈 페이로드를 생성할 수 있습니다.
- 서버가 응답으로 페이로드를 보내지 않으려면 대신 [HTTP 상태 204(No Content)](https://restfulapi.net/http-status-204-no-content/) 를 보내야 합니다.
- 기본적으로 200(OK) 응답(헤더 및 페이로드)은 **캐시 가능** 합니다. 채칭을 재정의해야 하는 경우 응답에는 캐시 각각의 [캐시 헤더](https://restfulapi.net/caching/) 가 포함되어야 합니다.



---



### **201 (Created)**

HTTP 상태 201은 `HTTP POST`요청 결과 서버에 **하나 이상의 새 리소스가 성공적으로 생성** 되었음을 나타냅니다.



#### 1. HTTP 헤더

`Location`응답에는 새로 생성된 리소스에 대한 참조를 가질 수 있는 HTTP 헤더 목록의 헤더 필드에 URI가 포함될 수 있습니다.

또한 응답 페이로드에는 사용자 또는 사용자 에이전트가 가장 적합한 것을 선택할 수 있는 리소스 특성 및 위치 목록을 포함하는 엔터티가 포함될 수 있습니다.

`Content-Type`엔터티 형식은 헤더 필드 에 지정된 미디어 유형으로 지정됩니다.

> 원본 **서버는 201 상태 코드를 반환하기 전에 리소스를 생성해야 합니다** . 조치를 즉시 수행할 수 없는 경우 서버는 `202 (Accepted)` 응답으로 응답해야 합니다.



#### 2. 업데이트 유실 문제

`HTTP 201` 응답 은 방금 생성된 요청 변형에 대한 엔터티 태그의 현재 값을 나타내는 `ETag` 응답 헤더 필드를 포함할 수 있습니다

`ETag`헤더 필드는 "업데이트 손실" 문제를 방지하기 위해 이후의 조건부 요청에서 사용할 수 있습니다.

> 업데이트 손실 문제는 여러 사람이 서로의 변경 사항을 알지 못한 채 리소스를 편집할 때 발생합니다.

이 시나리오에서는 리소스를 업데이트하는 마지막 사람이 "승리"하고 이전 업데이트는 손실됩니다.

ETag는 `If-Match`헤더와 함께 사용되어 리소스를 업데이트해야 하는지 서버에서 결정할 수 있습니다.

일치 하지 않으면 서버는 `412 (Precondition Failed)`응답 `ETag`을 통해 클라이언트에 알립니다 .



---



### **202 (Accepted)**

**HTTP 상태`202`** 는 **요청이 처리를 위해 수락되었지만 처리가 완료되지 않았음**을 나타냅니다 . 이 상태 코드는 실제 작업이 본질적으로 비동기일 때 유용합니다.

그 목적은 프로세스가 완료될 때까지 서버에 대한 사용자 에이전트의 연결을 지속할 필요 없이 서버가 일부 다른 프로세스(아마도 하루에 한 번만 실행되는 배치 지향 프로세스)에 **대한 요청을 수락 할 수 있도록 하는 것입니다.**



#### 1. 응답 엔티티

이 응답과 함께 반환된 엔터티는 요청의 현재 상태를 설명하고 사용자에게 요청이 이행될 시기의 추정치를 제공할 수 있는(또는 포함하지 않은) 상태 모니터를 가리켜야 합니다(또는 내장해야 합니다).



#### 2. HTTP 상태 202 – 예

장기 실행 비동기 작업을 REST API에 제출한 경우 API는 다음과 같은 결과를 반환할 수 있습니다.

```
HTTP STATUS 202 (Accepted)

{
	"task": {
		"href": "/api/company/job-management/jobs/2130040",
		"id": "2130040"
	}
}
```



이제 사용자 에이전트는 작업 완료 상태에 대해 **HTTP GET** 요청을 URI /api/company/job-management/jobs/2130040에 주기적으로 보낼 수 있습니다.

위 API의 응답은 실제 예정된 작업의 현재 상태를 알려줍니다.



##### 2.1. 작업이 시작되지 않음

```
{
	"job" : {
		"@uri" : "/api/company/job-management/jobs/2130040" ,
		"id" : "2130040",
		"name" : "Update Resource",
		"job-state" : "SCHEDULED",
		"job-status" : "UNDETERMINED",
		"percent-complete" : "0",
		"scheduled-start-time" : "01-01-2013 10:50:45 PM GMT",
		"start-time" : "",
		"end-time" : "",
		"owner" : "Admin",
		"summary" : "random text"
	}
}
```



##### 2.2. 작업 시작됨

```
{
	"job" : {
		"@uri" : "/api/company/job-management/jobs/2130040" ,
		"id" : "2130040",
		"name" : "Update Resource",
		"job-state" : "STARTED",
		"job-status" : "INPROGRESS",
		"percent-complete" : "30",
		"scheduled-start-time" : "01-01-2013 10:50:45 PM GMT",
		"start-time" : "01-01-2013 10:50:55 PM GMT",
		"end-time" : "",
		"owner" : "Admin",
		"summary" : "random text"
	}
}
```



##### 2.3. 작업 완료

```
{
	"job" : {
		"@uri" : "/api/company/job-management/jobs/2130040" ,
		"id" : "2130040",
		"name" : "Update Resource",
		"job-state" : "COMPLETED",
		"job-status" : "SUCCESS",
		"percent-complete" : "100",
		"scheduled-start-time" : "01-01-2013 10:50:45 PM GMT",
		"start-time" : "01-01-2013 10:50:55 PM GMT",
		"end-time" : ""01-01-2013 10:52:18 PM GMT",
		"owner" : "Admin",
		"summary" : "random text"
	}
}
```

위의 예는 참고용입니다.



---



### **204 (No Content)**



**HTTP 상태 204(콘텐츠 없음)** 는 서버가 요청을 성공적으로 수행했으며 응답 페이로드 본문에 보낼 콘텐츠가 없음을 나타냅니다.

서버는 엔티티 헤더의 형태로 업데이트된 메타 정보를 반환하기를 원할 수 있습니다. 존재하는 경우 현재 문서의 활성 보기에 적용해야 합니다.

204 응답 **은 메시지 본문** 을 포함해서는 안 되므로 항상 헤더 필드 다음의 첫 번째 빈 줄로 종료됩니다.



#### 1. Cacheable

기본적으로 `204 (No Content)`응답은 **캐시 가능합니다. 캐싱을 재정의해야 하는 경우** 응답에는 캐시 각각의 [캐시 헤더](https://restfulapi.net/caching/) 가 포함되어야 합니다 .

예를 들어 `204 (No Content)`요청 페이로드가 앞뒤로 이동할 수 없을 만큼 충분히 큰 UPDATE 작업에서 상태를 반환할 수 있습니다. 사용자 에이전트는 리소스를 업데이트하기 위해 서버에 페이로드를 보냅니다.

`204`작업이 성공하면 서버는 클라이언트 응용 프로그램이 UI를 업데이트하여 사용자에게 작업 성공을 알릴 수 있도록 성공을 나타내는 응답을 합니다.

또한 분산 버전 제어 시스템과 같이 자동화된 데이터 전송이 널리 보급될 것으로 예상되는 인터페이스와 함께 자주 사용됩니다.



#### 2. 업데이트 유실 문제

상태 가 **204 인 경우 서버는 업데이트** **손실 문제**`ETag` 를 방지하기 위해 서버에서 추가 업데이트를 수행하기 전에 클라이언트가 클라이언트 측 리소스 표현을 확인할 수 있도록 HTTP 헤더를 포함할 수도 있습니다 .

업데이트 손실 문제 **는 여러 사람이 서로의 변경 사항을 알지 못한 채 리소스를 편집할** 때 발생합니다 .

이 시나리오에서는 리소스를 업데이트하는 마지막 사람이 "승리"하고 이전 업데이트는 손실됩니다.

ETag는 If-Match 헤더와 함께 사용하여 리소스를 업데이트해야 하는지 서버에서 결정할 수 있습니다. ETag가 일치하지 않으면 서버는 `412 (Precondition Failed)`응답을 통해 클라이언트에 알립니다.



---



### **301 (Moved Permanently)**

> HTTP 상태 301은 리디렉션 관련 상태 중 하나로 요청한 리소스가 `Location`헤더 에서 지정한 URL로 영구적으로 이동되었음을 나타냅니다 . 그리고 이후의 모든 요청은 새 URI를 사용해야 합니다.

리디렉션은 한 URL에서 다른 URL로 요청을 전달하는 프로세스입니다. 상태 301에 대한 사양은 리디렉션이 수행될 때 요청 메서드(및 요청 본문)가 변경되지 않도록 요구합니다.

GET 또는 HEAD 메서드에만 HTTP 301을 사용하는 것이 좋습니다.



#### 1. URL을 영구적으로 이동해야 하는 이유는 무엇입니까?

일반적으로 리소스의 URL을 변경하는 것은 바람직하지 않습니다. 그래도 자원의 URL을 변경해야 하는 피할 수 없는 상황에 직면할 수 있습니다.

그러한 몇 가지 예는 다음과 같습니다.

- HTTP에서 HTTPs 프로토콜로 리소스 이동
- 리소스가 할인되었으며 새 URL에서 대체 리소스를 사용할 수 있습니다.



#### 2. 위치 헤더

서버는 `Location`리소스의 새 위치를 포함하는 응답에 헤더 필드를 생성해야 합니다.



##### 2.1. 클라이언트 요청

```
GET /index.php HTTP/1.1
Host: www.example.com
```



##### 2.2. 서버 응답

```
HTTP/1.1 301 Moved Permanently
Location: https://example.com/index.asp
```



#### 3. Cachable

301 응답은 기본적으로 캐시 가능합니다. (즉, 메소드 정의 또는 명시적 캐시 제어에 의해 달리 표시되지 않는 한.)

자세한 내용은 이러한 [캐시 헤더](https://restfulapi.net/caching/) 를 참조하십시오.



#### 4. 응답 처리

- 클라이언트에 링크 편집 기능이 있는 경우 요청된 URL에 대한 모든 참조를 새 URL로 업데이트해야 합니다.
- 검색 엔진(Google 및 Bing)은 검색 결과에서 이전 URL을 대체하며 이전 URL은 결국 사라집니다. 링크 주스는 이전 URL에서 새 URL로 전달됩니다.
- 브라우저는 새 위치 URL을 읽고 요청을 새 위치로 리디렉션한 후 자동으로 301 응답 코드를 감지합니다.