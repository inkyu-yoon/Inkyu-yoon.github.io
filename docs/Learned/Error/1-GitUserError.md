---
layout: post
title: "· 【Git】 user.name & user.email 변경 오류"
nav_order: 1
parent : Error
grand_parent: 📚Learned
permalink: docs/Learned/Error/GitUserError
---

# Git user.name / user.email 변경 문제 해결 방법

<br>

## 이미 Git에 입력해둔 user.name 과 user.email를 변경하려고 했지만, 계속해서 오류가 발생했다.

<br>

------

```
git config --global user.name "name"
git config --global user.emial "email@mail.com"
```

위 명령어를 쳤을 때 오류가 생긴다면

```
git config --global --replace-all user.name "name"
git config --global --replace-all user.email "email@mail.com"
```

이렇게 입력하면 해결이 된다.

------

출처 : https://stackoverflow.com/questions/4310974/more-than-one-value-for-the-key-user-name-git

