---
layout: post
title: "· jwt 토큰 저장 위치 LocalStorage vs Cookie"
nav_order: 5
parent : TIP
grand_parent: 📚Learned
permalink: docs/Learned/TIP/OauthLogin
---

# jwt 토큰 저장 위치 LocalStorage vs Cookie
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


jwt는 개인정보나 다름없기 때문에, 더 좋은 위치에 저장하는게 맞는것 같아서 어디에 저장하는게 좋을지 고민해보았다.

<br>

## 1. localStorage에 저장

<br>


### 장점

<br>


- CSRF 공격에 안전하다. (이미지를 클릭하면 게시글을 쓰게끔 조작한뒤, 피해자는 웹 상 그냥 이미지인 줄 알고 눌렀더니, 피해자가 요청하지도 않은 게시글이 쓰이는 경우)
    - 자동으로 request에 담기는 쿠키와는 다르게 js코드에 의해서 헤더에 담기므로

<br>

### 단점

<br>

- XSS에 취약하다. (code injection attack : 공격자가 악의적인 js 코드를 피해자 웹 브라우저에서 실행시키는 것 )
    - 공격자가 localStorage에 접근하는 코드 한 줄만 주입하면 localStorage를 내 집 처럼 드나들수 있다.

<br>

## 2. Cookie에 저장

<br>

### 장점

<br>


- localStorage에 비해 XSS 공격으로부터 안전하다.
    - 쿠키의 httpOnly 옵션을 사용하면 JS로 쿠키에 접근할 수 없다.
    - 단, 100% 안전한 것은 아니다. 쿠키의 내용을 볼 수 없을 뿐이지, 항상 자동으로 request에 담기는 쿠기의 특성상 요청을 위조할 수는 있다.

<br>

### 단점

<br>

- CSRF 공격에 취약하다.
    - 자동으로 request에 담아서 보내기 때문에, 공격자가 request url만 안다면 사용자 관련 link를 클릭하도록 유도하여 request를 위조하기 쉽다.

<br>

{: .note-title }
> 결론
> 
> 보안상으로는 samesite 속성에 strict나 lax 라는 설정을 주면 서버만이 토큰을 저장할 수 있도록 할 수 있고
>
> 또한, secure 속성으로 https 통신에서만 쿠키를 사용할 수 있도록 할 수 있다고 한다.
>
> 일단은 쿠키에 저장을하고 추가적으로 할 수 있는 설정들을 해주어 정보를 노출하지 않는 편이 더 보안이 강화된것이라고 생각이 든다.