---
layout: post
title: "· HTTP 메서드의 특징"
nav_order: 4
parent : HTTP
grand_parent: 📚Learned
permalink: docs/Learned/HTTP/HTTPMethod
---

# HTTP 메서드의 특징
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


**URI**는 리소스만 식별하면 되지, 행위를 알 필요는 없다. 행위는 HTTP 메서드를 통해 식별한다.



## HTTP 메서드 종류
<br>

- GET: 리소스 조회
- POST: 요청 데이터 처리 (주로 등록에 사용)
- PUT: 리소스를 대체, 해당 리소스가 없으면 생성 (덮어쓰는 느낌)
- PATCH: 리소스 부분 변경
- DELETE: 리소스 삭제



### GET
<br>

- 리소스를 조회한다. 서버에 전달하고 싶은 데이터는 query()를 통해서 전달한다.
- **데이터를 조회**하고 싶을때 GET메세지를 전달하면, 원하는 데이터를 응답받는다.
- **응답 결과 리소스를 캐시해서 사용할 수 있다.**

### POST

- 메세지 바디를 통해 서버로 요청 데이터를 전달한다. 그러면 서버는 요청 데이터를 처리한다.
  주로, 신규 리소스 등록, 프로세스 처리 등에 사용된다.(단순히 값을 변경하는 것을 넘어 프로세스의 상태를 변경)
- 💡 **응답 서버로부터 URI가 지정**된다.
- 다른 메서드들과는 다르게, 여러번 호출하게 되면, 다른 결과를 일으킬 수 있다.

### PUT

- 리소스가 이미 있으면 기존 리소스는 삭제하고 대체한다. 없으면 생성 (덮어 쓰기, 부분적으로 수정은 할 수 없다.)
- 💡 **클라이언트가 리소스의 위치를 알고 URI를 지정한다.**

### PATCH

- PUT과 다르게, 부분적으로 리소스를 변경할 수 있다.

### DELETE

- 리소스를 제거한다.



## HTML FORM 사용
<br>

```html
<form action = "/save" method = "post"> 
    <input type = "text" name = "username" />
    <input type = "text" name = "age" />
    <button type = "submit">
        전송
    </button>
</form>
```

위와 같은 HTML의 form tag를 사용해서 데이터를 전송해야하는 경우가 있을 수 있다.

- HTML FORM은 **GET, POST**만 지원한다. form 태그 첫 줄에 `method` 를 지정하면 된다.

- 파일을 전송하고 싶은 경우에는 `<form action = "/save" method = "post" enctype="multipart/form-data">` 와 같이 enctype을 추가해주어야 한다.

- HTML FORM을 사용해서 delete 와 같은 동작을 하기 위해서는 AJAX 같은 **컨트롤 URI**을 활용해서 해결한다. (동사를 직접 사용)
  -> /new, /edit, /delete ...

  예시) /members/{id}/delete

<br>

참고하면 좋은 URI 설계 개념 : https://restfulapi.net/resource-naming/

-> 시간 있을 때, 번역해서 참고해봐야겠다.



