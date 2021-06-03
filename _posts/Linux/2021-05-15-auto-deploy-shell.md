---
layout: post
title:  "배포 자동화 쉘 스크립트"
excerpt: ""

categories:
  - etc
tags:
  - [Shell]

toc: true
toc_sticky: true
comments: true
 
date: 2021-05-15
last_modified_at: 2021-05-15
---
```bash
#!/usr/bin/bash
#!/bin/bash

cd airbnb
cd BE

echo "fetch 시작"

git fetch

// 변경사항 체크
LOCAL=`git rev-parse HEAD`
REMOTE=`git rev-parse origin/dev/BE`        

if [[ $LOCAL == $REMOTE ]]; then
        echo "빌드할 필요가 없습니다."
        exit 0
fi

echo "merge 시작"

git merge

echo "Kill tomcat"

// 프로세스 포트 번호 알아내기
BOOT=`jps | grep BE-0.0.1| cut -d " " -f1`

kill $BOOT

echo "빌드 시작"

./gradlew build -x test

java -jar build/libs/BE-0.0.1-SNAPSHOT.jar & #크론탭을 쓰려면 로그는 크론탭에서

java -jar build/libs/baseball-0.0.1-SNAPSHOT.jar & >> log.txt 2>&1
```

