---
layout: post
title:  "gradle 프로젝트에서 JUnit Test시 에러 발생"
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

### IntelliJ의 Gradle 프로젝트에서 평소와 같이 JUnit Test를 실행시켰다.

- 아래와 같은 에러가 발생했다.

```
FAILURE: Build failed with an exception.
* What went wrong:
Execution failed for task ':test'.
> No tests found for given includes: [net.honux.chess.PawnTest.test]
```

- Gradle프로젝트에서 JUnit을 실행시키기 위해서는 build.gradle 파일에 test라는 task를 추가해줘야 한다고 한다.

### Gradle Docs를 참조하여 build.gradle에 Test Task를 추가해 보았다.

- Gradle Docs에서 JUnit을 사용하려면 build.gradle 파일에 아래 내용을 추가하라고 한다.

	```
test {
    useJUnitPlatform()
}
```

- 이번엔 아래와 같은 에러가 발생했다. (useJUnitPlatform() 를 인식못한다.)

	```
Caused by: org.gradle.internal.metaobject.AbstractDynamicObject$CustomMessageMissingMethodException:  
Could not find method useJUnitPlatform() for arguments [] on task ':test' of type org.gradle.api.tasks.testing.Test.
```



### 현재 프로젝트의 gradle wrapper에 있는 gradle 버전이 문제였다.

- 프로젝트 폴더 - gradle - wrapper - gradle-wrapper.properties 에서 현재 프로젝트의 gradle 버전을 확인할 수 있다.
- 나는 gradle 버전이 3.4.1이었다 ㅠㅠ
- 위에 추가하려던 내용은 6.8.2 버전에서 사용할 수 있는 명령어였다.

<br>

# 해결방법

## 1. Gradle 버전 변경후 build.gradle 파일 수정

**1. Gradlew 명령어를 통한 버전 변경**

Gradle 버전을 변경하려는 프로젝트로 이동한 후 Gradlew 명령어를 통해 버전을 변경한다.

```
# ./gradlew wrapper --gradle-version [변경버전]
```

![img](https://blog.kakaocdn.net/dn/2V5hL/btqDdIBnh68/0GMUJKgxSnwr8AV5WuMA9K/img.png)

**2. 변경된 버전** 

변경 완료 후 gradle/wrapper/gradle-wrapper.properties에서 변경된 버전을 확인한다.

![img](https://blog.kakaocdn.net/dn/d1EMdT/btqDbFZViw7/cXSAm9K5V7gOEGvuyMt3ok/img.png)

- 출처 [https://twofootdog.tistory.com/56](https://twofootdog.tistory.com/56)

**3. build.gradle 파일에 아래 내용 추가**

```
dependencies {
    testCompile("org.junit.jupiter:junit-jupiter-api:5.6.0")
    testRuntime("org.junit.jupiter:junit-jupiter-engine")
}

test {
    useJUnitPlatform()
}
```

<br>

## 2. 3.4.1버전에 맞는 내용 build.gradle에 추가하기

- 3.4.1 버전에 맞는 test task코드를 추가하려 했으나, JUnit 5는 gradle 4.7이상부터 지원한다고 한다..

<br>

## 3. Project Setting에서 Gradle이 아닌 IntelliJ를 사용하여 test하도록 수정하기

- Preferences(설정) -> Build, Execution, Deployment -> Gradle에서 [Run tests using:] 항목을 IntelliJ로 수정한 후 실행시켜준다.
- 이렇게 하는 것은 gradle 프로젝트의 사용 목적에 맞지 않아 추천하지 않는다고 한다.

> That's "plan B", but Gradle won't rebuild that from scratch. Said otherwise, if you count on Gradle so someone else can use your project, don't do that, or make sure to push the IntelliJ project in your repo, but that defeats the purpose of using Gradle...

- 출처 - [https://stackoverflow.com/questions/30474767/no-tests-found-for-given-includes-error-when-running-parameterized-unit-test-in](https://stackoverflow.com/questions/30474767/no-tests-found-for-given-includes-error-when-running-parameterized-unit-test-in)


```