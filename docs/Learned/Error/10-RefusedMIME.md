---
layout: post
title: "· 스크립트 태그 상대경로 설정으로 인한 테마 에러"
nav_order: 10
parent : Error
grand_parent: 📚Learned
permalink: docs/Learned/Error/RefusedMIME
---

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230128143351819.png" alt="image-20230128143351819" style="zoom:80%;" />
</p>

```
Refused to apply style from 'http://localhost:8080/academy/assets/css/nucleo-icons.css' 
because its MIME type ('application/json') is not a supported stylesheet MIME type, 
and strict MIME checking is enabled.
```

위와 같은 에러가 발생하면서, css파일을 불러오지 봇했고 결과적으로 ui 테마가 깨지는 현상이 일어났다.

특히 `localhost:8080/academy/studet` 와 같은 url 에서는 에러가 없었지만 특이하게

`localhost:8080/academy/studet/register` 와 같이 `/` 가 3개인 url에서만 문제가 발생했다.

```
<script src="../assets/js/core/popper.min.js"></script>
<script src="../assets/js/core/bootstrap.min.js"></script>
<script src="../assets/js/plugins/perfect-scrollbar.min.js"></script>
<script src="../assets/js/plugins/smooth-scrollbar.min.js"></script>
<script src="../assets/js/material-dashboard.min.js?v=3.0.4"></script>
```

원래 html 에서 script 태그는 위와 같이 작성되어있었는데. `../assets` 상대 경로를 사용해서 에러가 발생했던 것 같다.

처음에는

타입 문제인줄 알고

```
<script type="application/json" src="../assets/js/core/popper.min.js"></script>
<script type="application/json" src="../assets/js/core/bootstrap.min.js"></script>
<script type="application/json" src="../assets/js/plugins/perfect-scrollbar.min.js"></script>
<script type="application/json" src="../assets/js/plugins/smooth-scrollbar.min.js"></script>
<script type="application/json" src="../assets/js/material-dashboard.min.js?v=3.0.4"></script>
```

위와 같이 했는데, 해결이 안되었었다.

결론적으로는

```java
<script src="/assets/js/core/popper.min.js"></script>
<script  src="/assets/js/core/bootstrap.min.js"></script>
<script  src="/assets/js/plugins/perfect-scrollbar.min.js"></script>
<script  src="/assets/js/plugins/smooth-scrollbar.min.js"></script>
<script src="/assets/js/material-dashboard.min.js?v=3.0.4"></script>
```

경로를 절대경로로 바꾸어 문제를 해결할 수 있었다.