---
layout: post
title: SubSystem
category: S/W Architecture
permalink: /sw-architecture/:year/:month/:day/:title/

tag: [설계, 모듈뷰, SubSystem]
---

# 서브 시스템

시스템의 모듈 뷰를 문서화할 때 일정하게 조합된 모듈을 서브 시스템이라고 식별할 수 있다.

서브 시스템은 보통 다음과 같은 경우 사용할 수 있다.

* 전체 시스템에서 기능적인 밀집성이 있는 서브 집합
* 독립적으로 실행 가능
* 점증적으로 개발되고 배포될 수 있음

<br>

## 서브 시스템의 예시

예를 들어, 화성 탐사 로봇의 소프트웨어는 다음과 같은 서브 시스템으로 분할 할 수 있다.

* 커뮤니케이션
* 모션
* 전력 관리
* 주행
* 상태 모니터링

<br>

## 라이브러리 != 서브 시스템

로봇 시스템내에서 수학 유틸리티 라이브러리가 있다고 가정하자. 
이 경우 라이브러리는 시스템의 부분이며, 모듈의 조합이고 밀집성있는 기능을 갖고 있다. 
하지만 시스템의 목적의 일부가 되는 작업을 독립적으로 수행할 수 없기 때문에
라이브러리를 서브 시스템이라고 하지는 않는다.

<br>

## 서브 시스템의 활용

서브 시스템은 `독립적인` 특성을 갖고 있다. 이 특성 덕분에 서브 시스템을 다음과 같은 경우에
활용할 수 있다.

* 서브 시스템의 성능 검토를 분석가에게 요청 가능
* 서브 시스템을 패키지로 만들어 다른 팀에게 구현하도록 넘겨 줄 수 있음
* 테스트도 독립적으로 할 수 있음

<br>

## UML

서브 시스템은 `UML`에서 `<<subsytem>>`과 같은 스테레오 타입으로 표현할 수 있다.
서브 시스템은 모듈(구현 단위)의 그룹 또는 런타임 존재를 갖는 컴포넌트의 그룹을 표현할 수 있다.
