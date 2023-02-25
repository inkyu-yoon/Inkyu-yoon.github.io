---
layout: post
title: "· QueryDsl를 이용한 쿼리 수와 실행시간 개선 여정기"
nav_order: 11
parent : JPA
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/JPA/QuerydslRepo
---
# QueryDsl를 이용한 쿼리 수와 실행시간 개선 여정기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


<br>

## 개선하기로 한 이유

<br>

개인 프로젝트와 팀 프로젝트를 진행하면서, 아쉬웠던 점이 하나 있었다.

열심히 Querydsl 강의를 들었지만, 활용하지 못했다는 것..

핑계일 수 있겠지만, 일단 결과물을 완성시켜야 한다는 생각이 앞서서 쿼리문을 잔뜩 남발했다.

'쿼리문을 여러개 날려도 원하는 결과만 반환되면 되니까?!' 라고 생각했었다. 🤔



지금은 프로젝트가 다 끝났고, 좀 여유가 생겨서 꼭 리팩토링하자고 다짐했었다!

먼저 문제점을 살펴보자.



내가 진행했던 SNS 프로젝트에서 알람을 조회하는 기능이 있었다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230225210348237.png" alt="image-20230225210348237" style="zoom:80%;" />
</p>

위 기능은, 내가 작성한 글에 댓글이나 좋아요를 다른 사용자가 클릭했을 때 목록을 가져와서 최신 알림 순으로 보여주는 기능이다.

그렇다면, 이 기능을 위해서 몇개의 쿼리를 날렸었을까?

<br>

## 코드적 차이와 Querydsl을 이용한 개선

<br>

```java
    public Page<AlarmListDetailsDto> getDetailAlarms(String requestUserName, Pageable pageable) {
        //user 유효성 검사하고 찾아오기
        User requestUser = userRepository.findByUserName(userName)
                .orElseThrow(() -> new SNSAppException(ErrorCode.USERNAME_NOT_FOUND));

        Page<Alarm> alarms = alarmRepository.findByUser_IdOrderByCreatedAtDesc(requestUser.getId(), pageable);
        List<AlarmListDetailsDto> alarmsDto = new ArrayList<>();


        //post id와 user id가 아닌 post title 과 userName 을 넣기 위해..for Each 사용
        for (Alarm alarm : alarms) {
            Long fromUserId = alarm.getFromUserId();
            Long postId = alarm.getTargetId();

            String fromUserName = userRepository.findById(fromUserId)
                    .orElseThrow(() -> new SNSAppException(ErrorCode.USERNAME_NOT_FOUND)).getUserName();

            String title =  postRepository.findById(postId)
                    .orElseThrow(() -> new SNSAppException(ErrorCode.POST_NOT_FOUND)).getTitle();

            alarmsDto.add(new AlarmListDetailsDto(alarm, fromUserName, title));

        }

        log.info("🔔알림 조회 끝 userName : {}");

        return new PageImpl<>(alarmsDto);
    }
```

원래 로직은 위와 같았다.

먼저, 알람 조회를 요청한 **①사용자를 DB에서 조회**하고, 그 사용자의 기본키에 해당하는 **②알람을 모두 조회**한 후

그 알람의 갯수만큼 for문을 돌려서 알람 엔티티가 갖고있는 알람을 발생시킨 사용자의 id와 게시글 id를 이용해서

**③알람을 발생시킨 DB에서 사용자를 조회**하고 **④알람이 발생한 게시글을 DB에서 조회**해서 dto를 구성했다..

단순히 코드로만 봐도 최소 4번의 쿼리를 날리는데,

사실 사용자의 수와 게시글의 수가 늘어나면 쿼리문이 2개씩 더 늘어나는 엄청난 나비효과가 있었다..


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230225211848955.png" alt="image-20230225211848955" style="zoom:67%;" />
</p>

알람을 발생시킨 회원이 한명 늘자, 쿼리문이 하나 증가했다.

만약 게시글이 증가하면, 또 쿼리문의 수가 계속 늘어날 것이다..

같은 조건에서 QueryDsl로 개선한 로직을 적용하니

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230225212215767.png" alt="image-20230225212215767"  />
</p>

놀랍게도 한개의 쿼리문으로 같은 반환값을 얻어낼 수 있었다.



```java
package likelion.sns.repository;

import com.querydsl.core.group.GroupBy;
import com.querydsl.core.types.Projections;
import com.querydsl.jpa.impl.JPAQueryFactory;
import likelion.sns.domain.dto.alarm.AlarmListDetailsDto;
import likelion.sns.repository.custom.AlarmCustomRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

import static likelion.sns.domain.entity.QAlarm.alarm;
import static likelion.sns.domain.entity.QPost.post;
import static likelion.sns.domain.entity.QUser.user;

@Repository
public class AlarmCustomRepositoryImpl implements AlarmCustomRepository {

    private final JPAQueryFactory jpaQueryFactory;

    public AlarmCustomRepositoryImpl(JPAQueryFactory jpaQueryFactory) {
        this.jpaQueryFactory = jpaQueryFactory;
    }


    @Override
    public List<AlarmListDetailsDto> getAlarmListByUserId(Long userId) {
        List<AlarmListDetailsDto> result = jpaQueryFactory.from(alarm)
                .where(alarm.user.id.eq(userId))
                .join(user).on(user.id.eq(alarm.fromUserId))
                .join(post).on(post.id.eq(alarm.targetId))
                .orderBy(alarm.createdAt.desc())
                .transform(GroupBy
                        .groupBy(alarm.id).list(
                                Projections.constructor(AlarmListDetailsDto.class,
                                        alarm, user.userName, post.title)));

            return result;
    }
}

```



작성한 QueryDsl 로직은 위와 같다.

먼저, 알람 조회를 요청한 사용자의 id(기본키)로 alarm 데이터를 모두 가져온 뒤, 알람 데이터가 갖고 있는 알람을 발생시킨 id와 user 테이블을 조인했고

그 다음 알람 데이터가 갖고 있는 알람이 발생된 게시글 id와 post 테이블을 조인한 뒤

`orderby` 를 사용해서 최신순으로 정렬하고

`transform()` 메서드와 `Projections.constructor()`를 사용해서 바로 DTO로 맵핑하였다.

위와 같이 쿼리문을 구성하니 1개의 쿼리문으로 원하는 결과를 얻을 수 있었다.

<br>

## 결과 분석

<br>

### 개선 전

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230225221358973.png" alt="image-20230225221358973" style="zoom:80%;" />
</p>

반환 결과는 위와 같다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230225221155928.png" alt="image-20230225221155928" style="zoom:80%;" />
</p>

쿼리문의 총 갯수는 `5`개이고 총 실행 시간은 `39+39+39+40+41`ms = `198` ms

<br>

### 개선 후

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230225221534112.png" alt="image-20230225221534112" style="zoom:80%;" />
</p>

반환 결과는 당연히 동일하다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230225221609187.png" alt="image-20230225221609187" style="zoom:80%;" />
</p>

쿼리문의 총 갯수는 `2`개이고 총 실행 시간은 `37+38`ms = `75ms`이다.

<br>

### 결론

<br>

알람을 발생시킨 유저 or 알람이 발생된 게시글의 수가 늘어나면

**개선 전의 경우 쿼리수는 더 늘어날 것이고 총 쿼리 실행시간도 계속 늘어날 것이다.**

실제로 다른 사용자가 좋아요를 한번 더 눌렀을 때, 개선 전의 경우 쿼리문 하나가 추가되었고 39msec 가 추가되었다.

<br>

하지만, 개선 후의 상황의 경우

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230225222331826.png" alt="image-20230225222331826" style="zoom:80%;" />
</p>

알람 조회 쿼리문의 실행 시간이 `38`ms 에서 `40`ms 로 아주 약간의 시간만 추가되었다.

<br>

{: .highlight }
참고 블로그 : [https://kobumddaring.tistory.com/59](https://kobumddaring.tistory.com/59)
