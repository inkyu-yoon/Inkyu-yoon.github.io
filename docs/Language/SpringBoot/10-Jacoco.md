---
layout: post
title: "· Jacoco 라이브러리로 테스트 코드 커버리지 개선하기"
nav_order: 10
parent : SpringBoot
grand_parent: 👩🏻‍💻Language
permalink: docs/Language/SpringBoot/Jacoco
---

먼저, `Jacoco` 에 대해서 알려준 오석이 땡큐!!

<br>

## Jacoco 란?

- Java VM 기반 환경에서 코드 커버지리 분석을 위한 라이브러리이다.

{: .note-title }
> 코드 커버리지란?
> 
> 소프트웨어의 테스트 케이스가 얼마나 충족되었는지를 나타내는 지표 중 하나이다.
> 1. **라인 커버리지** : 코드 한 줄이 한 번 이상 실행된다면 충족된다.
> 2. **결정 커버리지** : 브랜치 커버리지라고도 불린다. 모든 조건식의 내부 조건이 true/ false 를 가지게 되면 충족한다.
> 3. **조건 커버리지** : if 조건문 안의 개별 조건식이 false 인경우와 true인 경우 모두 실행되었을 때 충족한다. 결정 커버리지보다 더 detail한 커버리지이다.

<br>

```groovy
plugins {
	id 'java'
	id 'org.springframework.boot' version '2.7.5'
	id 'io.spring.dependency-management' version '1.0.15.RELEASE'

	//jacoco 테스트 커버리지 확인
	id 'jacoco'
}

```

<br>

plugin 에 위와같이 `jacoco` 를 추가해준다.

<br>

```groovy

test {
	useJUnitPlatform()// JUnit5를 사용하기 위한 설정
	finalizedBy 'jacocoTestReport'  // Test 이후 커버리지가 동작하도록 finalizedBy 추가
}

jacoco {
	toolVersion = '0.8.8'
}

jacocoTestReport {
	dependsOn test
	reports {
		html.enabled true
		xml.enabled false
		csv.enabled false

		//리포트 위치 지정
		html.destination file('build/reports/myReport.html')

	}

	finalizedBy 'jacocoTestCoverageVerification'
}



// 검증 단계
jacocoTestCoverageVerification {
	violationRules {
		rule {
			enabled = true // 활성화
			element = 'CLASS' // 클래스 단위로 커버리지 체크
			includes = [
			        '**/restController*',
					'**/service'
			]

			// 라인 커버리지 제한을 80%로 설정
			limit {
				counter = 'LINE'
				value = 'COVEREDRATIO'
				minimum = 0.80
			}

			// 브랜치 커버리지 제한을 80%로 설정
			limit {
				counter = 'BRANCH'
				value = 'COVEREDRATIO'
				minimum = 0.80
			}

			// 빈 줄을 제외한 코드의 라인수를 최대 200라인으로 제한
			limit {
				counter = 'LINE'
				value = 'TOTALCOUNT'
				maximum = 200
			}
			
		}
	}
}
```

 <br>

그리고 위 내용을 `build.gradle` 에 추가해준다.

`element = 'CLASS'`로 커버리지 카운터를 설정했다.

<br>

해당 프로젝트 경로로 `git bash` 터미널을 연 뒤,

```
./gradlew test
```

를 입력한다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230110155711626.png" alt="image-20230110155711626" style="zoom: 80%;" />
</p>

위와 같이 리포트가 생성된다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230110155756982.png" alt="image-20230110155756982"  />
</p>

설정한 대로 `>build>reports>myReport.html` 경로에 리포트가 생성된다.

리포트를 확인해서, 부족한 부분을 개선해서

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230110173510398.png" alt="image-20230110173510398" style="zoom: 50%;" />
</p>

Controller 테스트는 구문 커버리지는 90%이상으로 잘 작성했다.

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230110210729377.png" alt="image-20230110210729377" style="zoom: 80%;" />
</p>

서비스는 테스트 코드를 짤게 산더미다..ㅎㅎ 

시간을 내서 완성해야겠다.

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230110211751704.png" alt="image-20230110211751704" style="zoom:80%;" />
</p>

그리고 `validation` 으로 바인딩 에러를 처리한 구문을 테스트 코드를 어떻게 짜야할지 아직 모르겠다..

이 부분도, 해결해봐야겠다.

{: .highlight }
> 참고 : [https://percyfrank.github.io](https://percyfrank.github.io/springboot/jacoco01/#5-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%A7%84%ED%96%89-%ED%9B%84-%EA%B2%B0%EA%B3%BC-%ED%99%95%EC%9D%B8)


