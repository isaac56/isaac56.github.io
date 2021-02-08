---
layout: post
title:  "gradle이란 무엇일까"
excerpt: ""

categories:
  - gradle
tags:
  - [gradle]

toc: true
toc_sticky: true
comments: true
 
date: 2021-02-08
last_modified_at: 2021-02-08

---

## Gradle이란?

- Gradle은 groovy 언어를 이용한 빌드 자동화 시스템이다. Ant와 Maven과 같은 이전 빌드 도구들의 장점을 모아 만든 오픈소스 빌드 도구이다.

- 프로젝트 설정이나 의존 라이브러리 등 빌드에 필요한 정보를 gradle로 설정해 놓으면, 다른 환경에서 프로젝트를 불러도 빌드에 필요한 것들을 알아서 설정해준다.

- 심지어 Maven의 pom.xml을 Gradle용으로 변환할 수도 있으며 Maven의 중앙저장소도 지원하기 때문에 필요한 라이브러리를 가져다 사용할 수 있다.



## gradle wrapper

1. 이미 존재하는 프로젝트를 새로운 환경에 설치할때 별도의 설치나 설정과정없이 곧 바로 빌드할 수 있게 해준다.

   (Java나 Gradle도 설치할 필요가 없다. gradle wrapper를 이용하면 로컬에 설치된 Java와 gradle 버전을 신경쓸 필요 없이 wrapper에 포함된 java와 gradle을 사용하는 듯 하다)

2. wrapper는 사용자가 Gradle이 설치되어 있지 않아도 Gradle tasks를 실행할 수 있도록 해주는 작은 script, jar 및 등록 정보 파일 
3. wrapper를 생성하면 사용자가 프로젝트를 만든 사람과 동일한 버전의 Gradle을 사용할 수 있음

- gradle wrapper 실행

```bash
$ gradle wrapper

BUILD SUCCESSFUL in 602ms
1 actionable task: 1 executed
```

아래와 같이 파일이 만들어짐

```bash
.
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
└── gradlew.bat

2 directories, 5 files
  $ ./gradlew build
```

Wrapper를 사용하면 위와 같이 실행가능

```bash
  $ gradle build
```

이 경우 Java나 Gradle이 설치되어 있어야 하고, 새로받은 프로젝트의 Gradle 버전과 로컬에 설치된 Gradle 버전이 호환되지 않으면 문제가 발생할 수 있음

또, gradle명령어로 컴파일이나 빌드 등을 할때, 위와 같이 하면 로컬에 설치된 gradle을 사용

- Build 후에는 test, compile 후 jar파일 생성
- gradlew라는 UNIX script와 gradlew.bat라는 Windows명령이 jar 파일 및 속성 파일과 함께 생성됨

출처: [https://velog.io/@hwany/gradle](https://velog.io/@hwany/gradle) 

