---
layout: post
title: "· axios post 요청 전달 시 authorization 토큰 인식 에러 해결"
nav_order: 3
parent : FrontEnd
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/FrontEnd/AxiosPost
---

# axios post 요청 전달 시 authorization 토큰 인식 에러 해결
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<br>


`axios` 로 만들어 둔 `RestController` 에 토큰과 함께 요청을 보내는데, 자꾸 토큰이 없다는 에러가 발생했다.

알고보니... 정말 사소한 부분 떄문에 에러가 발생했었다..

그 해결과정을 써보려고 한다.



## 수정하기 전

<br>


### controller

<br>


```java
  /**
     * 좋아요 입력하기 ( 한 계정당 한개만 가능)
     */
    @ApiOperation(value="좋아요 추가", notes="(유효한 jwt Token 필요), 한 계정당 한개만 누를 수 있다.")
    @PostMapping("/{postId}/likes")
    public Response giveLike(@PathVariable(name = "postId") Long postId, @ApiIgnore Authentication authentication) throws SQLException {
        String requestUserName = authentication.getName();
        log.info("좋아요 요청자 userName : {}", requestUserName);

        likeService.AddLike(postId,requestUserName);
        return Response.success("좋아요를 눌렀습니다.");
    }
```

위와 같이 post 요청을 받는 RestController 의 메서드가 있었다.

결론 먼저 말하면, controller 에 문제가 있었던 것은 아니었다.

controller 에 문제가 있었는 줄 알고 1 ~ 2 시간정도 삽질했던 것 같다..

<br>

### script 함수

<br>


```javascript
/** 좋아요 클릭 **/
        likeCreate: function () {

            let postId = document.querySelector(`#input-postId`).value;
            const con_check = confirm("좋아요를 누르시겠습니까?");
            const url = "/api/v1/posts/" + postId + "/likes";
            if (con_check === true) {
                axios.post(url,{
                            headers: {
                                'Content-Type': 'application/json',
                                'Authorization': `Bearer ${localStorage.getItem("token")}`
                            }
                        }
                ).then((response) => { }
                ).catch((error) => { });
            }
        }
```

<br>


위 script에 문제가 있었다.

참고로, 내가 구현한 좋아요 입력하기 controller 는 별도의 `@RequestBody` 어노테이션이 붙은 request Dto가 존재하지 않았다.

따라서

```javascript
axios.post(url,{
                            headers: {
                                'Content-Type': 'application/json',
                                'Authorization': `Bearer ${localStorage.getItem("token")}`
                            }
                        }
                )
```

위와 같이 url과 headers 만 추가해서 요청을 보냈었고 이게 문제였다.



```
axios.<method>( <요청할 url> , <body에 담을 json 데이터> , <옵션 사항(헤더 등등)>)
```

axios 요청은 기본적으로 위와 같은 파라미터 구조를 가지고 있었다.

내가 구현한, 좋아요 입력 post Controller 는 body 부분을 전혀 필요로 하지 않았기 때문에, 생략을 했었다.

즉, 위 구조에 따라 **나는 토큰 정보를 body에 담아서 요청을 하게 된 것이고, 따라서 토큰을 인식하지 못한 것이었다.**


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230105165005254.png" alt="image-20230105165005254" style="zoom: 67%;" />
</p>

에러 로그를 살펴보니, 요청한 data 부분에 내가 입력했던 헤더 옵션이 들어가 있었고


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230105165253105.png" alt="image-20230105165253105" style="zoom: 67%;" />
</p>

왼쪽 처럼, `Authorization` 에 토큰 정보가 있어야 하는데, 오른쪽 상황처럼 `Authorization` 정보가 전혀 보이지 않아서

왜 에러가 났는지 알게 되었다..

후..

<br>

## 해결

<br>

```javascript
/** 좋아요 클릭 **/
        likeCreate: function () {
            let postId = document.querySelector(`#input-postId`).value;
            const data = null;
            const con_check = confirm("좋아요를 누르시겠습니까?");
            const url = "/api/v1/posts/" + postId + "/likes";
            if (con_check === true) {
                axios.post(url, data,{
                            headers: {
                                'Content-Type': 'application/json',
                                'Authorization': `Bearer ${localStorage.getItem("token")}`
                            }
                        }
                ).then((response) => { }
                ).catch((error) => { });
            }
        }
```

<br>
위와 같이 코드를 변경하여 정상 동작 시킬 수 있었다.

<br>

`axios` 의 `delete` 요청시에는, body부분 파라미터를 생략해도 에러가 발생하지 않아서, 원인을 알아차리기 힘들었었다.

`put` 이나 `post` 의 경우에는 `@RequestBody` 로 맵핑할 request dto 가 없는 경우에도, 

위 코드와 같이 null 데이터를 추가해서 파라미터로 함께 넣어주어야 에러를 막을 수 있다.

