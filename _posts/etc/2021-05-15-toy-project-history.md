---
layout: post
title:  "개인 프로젝트 정리"
excerpt: ""

categories:
  - etc
tags:
  - [Toy Project]

toc: true
toc_sticky: true
comments: true
 
date: 2021-05-15
last_modified_at: 2021-05-15
---
[온라인 야구게임 iOS 앱]

- 소스 코드: https://github.com/isaac56/baseball
- 진행 기간: 2021.5.1 ~ 2021.5.14
- 주요 내용:
  - 사용자 두 명이 같은 방에서 1:1로 야구를 진행할 수 있는 게임 App입니다.
  - 야구 게임의 메인 로직은 Server 에서 계산하도록 구현하였습니다.
  - OAuth 인증으로 깃헙 로그인을 할 수 있습니다.
- 본인의 역할: DB 설계, 백엔드 API 서버 개발, AWS 배포
- 사용한 Skill 또는 지식:
  - Spring boot, Spring data jdbc, Mysql
  - Git, AWS EC2
  - OAuth, jwt
- 결과/성과:
  - OAuth와 jwt를 사용하여 토큰 기반 인증방식을 구현해보았습니다.
  - spring interceptor를 학습하고, 이를 사용하여 인증이라는 공통 기능이 api 호출 전에 실행되도록 하였습니다.

<br>

[반찬 판매 웹사이트 데모]

- 소스 코드: https://github.com/isaac56/sidedish
- 진행 기간: 2021.4.19 ~ 2021.4.30
- 주요 내용:
  - 반찬 판매 사이트를 흉내 낸 데모 웹사이트 입니다.
  - 반찬을 판매하기 위한 요구사항을 만들고 DB를 설계하여 API 서버를 구현하였습니다.
    1. 하나의 반찬은 여러 카테고리에 속할 수 있고, 여러 태그(특가 할인, 베스트 상품 등)를 가질 수 있습니다.
    2. 하나의 반찬은 여러 개의 이미지를 가지고 있습니다.
    3. 하나의 반찬은 하나의 판매 업체에 속하며, 판매 업체 별로 배송 정보를 갖고 있습니다.
  - OAuth 인증으로 깃헙 로그인을 할 수 있습니다.
- 본인의 역할: DB 설계, 백엔드 API 서버 개발, AWS 배포
- 사용한 Skill 또는 지식:
  - Spring boot, Spring data jdbc, Mysql
  - Git, AWS EC2
  - OAuth
- 결과/성과:
  - 웹 서버와 웹 어플리케이션 서버를 나눠 개발을 진행하면서 CORS error를 겪고, 이를 해결하는 과정에서 SOP와 CORS에 대하여 학습하였습니다.
  - OAuth 인증을 구현해보면서 OAuth 의 동작 원리를 학습하게 되었습니다.
  - Spring data jdbc를 사용하면서 연관 관계의 domain을 aggregate로 묶고 aggregate root 를 통해 접근하는 설계 방법을 연습해보았습니다.

<br>

[할 일 목록 관리 앱]

- 소스 코드: https://github.com/isaac56/todo-list
- 진행 기간: 2021.4.5 ~ 2021.4.16
- 주요 내용:
  - 해야 할 일, 하고 있는 일, 완료한 일 세 가지 항목을 일정에 등록할 수 있는 ios 앱입니다.
  - 일정의 순서를 drag&drop으로 수정할 수 있고, 서버에 저장하여 그 목록과 순서가 유지됩니다.
- 본인의 역할: DB 설계, 백엔드 API 서버 개발, AWS 배포
- 사용한 Skill 또는 지식:
  - Spring boot, Spring data jdbc, Mysql
  - Git, AWS EC2
  - Custom order 구현
- 결과/성과:
  - 사용자가 지정한 임의의 순서를 DB에 저장하는 방법을 고민하였습니다.
  - 임의의 순서를 바꿨을 경우 최대한 적은 쿼리로 그 순서를 저장하는 방법을 고민하고 적용하였습니다.

<br>

[Qna 게시판]

- 소스 코드: https://github.com/isaac56/spring-boot-qna
- 진행 기간: 2021.3.8 ~ 2021.3.26
- 주요 내용:
  - 간단한 회원가입과 로그인, 질문 글쓰기와 답변이 가능한 게시판 사이트입니다.
- 본인의 역할: 프론트 개발, 서버 개발, heroku 배포
- 사용한 Skill 또는 지식:
  - Spring boot, JPA
  - handlebars.java, ajax
- 결과/성과:
  - MVC 구조를 학습하여 각 역할을 이해하고, 적용하여 구현해보았습니다.
  - JPA의 lazy loading과 eager loading의 차이와 N+1 쿼리 문제를 학습하였습니다.
  - API 반환 데이터를 json으로 직렬화할 때 발생하는 순환 참조 문제를 겪고, 해결해보았습니다.

<br>

[반도체 설비 진단 프로그램]

- 진행 기간: 2020.1.2 ~ 2020.12.1
- 주요 내용:
  - 생산 중인 반도체 웨이퍼와 PCB의 불량 현황을 시각화하여 보여주는 프로그램
  - 생산 설비 부위별 불량 집중도를 시각화하여 보여주는 프로그램
- 본인의 역할: Window Client 프로그램 개발, UI 디자인
- 사용햔 Skill 또는 지식: C# Winforms
- 결과/성과:
  - Row by Row로 저장하던 DB 테이블의 데이터 분포를 확인하여, 테이블을 Simple 테이블과 Detail 테이블 두 개로 나눔으로써 프로그램의 조회 속도 향상
  - 생산 설비의 부위별 집중도를 시각화하여, 이상 설비 검출 시스템 구축

<br>

[개인 영화 추천 안드로이드 앱]

- 소스 코드: https://www.youtube.com/watch?v=wg1hso1KnME
- 진행 기간: 2018.8.1 ~ 2018.11.15
- 주요 내용: 사용자가 평가한 영화를 토대로 collaborative filtering 기법으로 사용자가 좋아할 만한 영화를 추천해주는 안드로이드 앱입니다.
- 본인의 역할: Android 어플리케이션 개발, 서버 개발
- 사용한 Skill 또는 지식:
  - Android (java)
  - Django
  - jsoup crawling
- 결과/성과:
  - Recycler View, Navigation Bar등을 사용하여 Android 어플리케이션을 만들어보는 경험을 해보았습니다.
    - JSOUP으로 네이버 영화를 크롤링하여 많은 영화정보를 가져올 수 있었습니다.