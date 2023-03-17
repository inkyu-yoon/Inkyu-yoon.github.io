---
layout: post
title: "· Docker, 같은 Network로 연결되어 있을 경우, 컨테이너 간 통신"
nav_order: 9
parent : Docker
grand_parent: 📚Learned
permalink: docs/Learned/Docker/DockerNetworkConnect
---

# Docker, 같은 Network로 연결되어 있을 경우, 컨테이너 간 통신

<br>

도커 교과서를 공부하면서 든 의문점을 해결했다.

자바로 만든 컨테이너, 자바스크립트로 만든 컨테이너, Go로 만든 컨테이너를

`docker create network nat` 이라는 네트워크를 만든 다음, 같은 네트워크 안에 연결시켜서 컨테이너를 실행했다.

<p align="cengter">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317170329816.png" alt="image-20230317170329816" style="zoom: 80%;" />
</p>

그렇게해서 위와 같이 총 3개의 컨테이너를 실행했고, 책에서는 go로 실행한 컨테이너가 자바로 실행한 컨테이너에 이미지를 요청해서 받아온다고 쓰여있었다.



<br>



```dockerfile
FROM diamol/golang AS builder

COPY main.go .
RUN go build -o /server

# app
FROM diamol/base
ENV IMAGE_API_URL="http://iotd/image" \
    ACCESS_API_URL="http://accesslog/access-log"

CMD ["/web/server"]

WORKDIR /web
COPY index.html .
COPY --from=builder /server .
RUN chmod +x server
```



근데 go 애플리케이션의 Dockerfile에는

```dockerfile
ENV IMAGE_API_URL="http://iotd/image" \
    ACCESS_API_URL="http://accesslog/access-log"
```

이런식의 url이 적혀있었다.

포트 번호가 쓰여있는 것도 아니고? 어떻게 컨테이너끼리 소통하는지 궁금해서 찾아보았다.



찾아보니, 같은 네트워크로 묶여있는 경우 IP 주소 대신 컨테이너 이름을 사용해서 요청하는 것이 가능해진다고 한다.

테스트로, iotd 이름을 가진 컨테이너를 `io` 로 바꿔서 실행한 뒤, Go 애플리케이션을 실행시켜보았더니 에러가 발생하였다.

<p align="cengter">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230317170329816.png" alt="image-20230317170329816" style="zoom: 80%;" />
</p>

따라서, 나는 자바스크립트 애플리케이션인 `access-log` 이미지를 내 마음대로 `acl` 이라는 이름으로 실행시켰는데, 

go 애플리케이션 dockerfile의 환경변수로는 `ACCESS_API_URL="http://accesslog/access-log"` 이 입력되어있으므로 

go 어플리케이션과 소통할 수 없는 상태인 것이었다..ㅎ

따라서, `ACCESS_API_URL="http://acl/access-log"` 로 수정한 뒤 다시 이미지를 빌드하고 실행하면 정상적으로 동작할 것이다.


{: .highlight }
참고 : [https://www.inflearn.com/questions/429828/%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EA%B0%84-%ED%86%B5%EC%8B%A0-%EC%A7%88%EB%AC%B8](https://www.inflearn.com/questions/429828/%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EA%B0%84-%ED%86%B5%EC%8B%A0-%EC%A7%88%EB%AC%B8)
