---
layout: post
title:  "마이그레이션 방법 (CDC, dual write)"
excerpt: ""

categories:
  - Mysql
tags:
  - [Mysql]

toc: true
toc_sticky: true
comments: true
 
date: 2021-08-20
last_modified_at: 2021-08-20

---

## 마이그레이션

online database migration을 간단히 생각하면, 기존의 데이터를 다른 곳으로 이동시키는 것이라고 할 수 있다.

이건 테이블 간, 데이터베이스 간, 다른 물리적인 저장장치 간 등 다양한 범주로 생각할 수 있는데, 

실제 사용자들이 접근하고 있는 서비스 DB의 경우엔, 

사용자 접근에 영향을 주지 않은 채, source database와 target database 간의 데이터 일관성을 유지하는게 관건이라고 할 수 있다.

## 온라인 마이그레이션의 조건

마이그레이션이 지켜야할 조건을 러프하게 정의하면 다음과 같다.



1. **완전해야 한다.**
   - source database의 모든 data가 마이그레이션 되어야 한다.
2. **일관성이 있어야 한다.**
   - target database는 source database와 완전히 일치해야 한다.(no data loss, no data duplicates, and no transactions reordering)
3. **반복가능해야 한다.**
   - 예상치 못한 장애 등을 대응하기 위하여, 마이그레이션은 처음부터 다시 실행될 수 있어야한다.
4. **클라이언트 동작이나 코드에 영향이 없어야 한다.**
   - 마이그레이션이 진행되는 동안 클라이언트 동작에 영향이 가거나, 마이그레이션으로 인해 클라이언트 코드가 바뀌어선 안된다.
5. **테스트가 가능해야한다.**
   - 특히 source database 스키마와 target database 스키마가 다른 경우, source database에 접근하는 클라이언트에 영향을 주지않은채로 테스트가 가능해야 한다.



## CDC (Change Data Capture) 방법

<img src="https://miro.medium.com/max/452/0*yDad9BbVnjuU8VKt" style="zoom:150%;" />



CDC 방법은 Transaction log를 이용하는 방법이다.

최초엔 source database의 snapshot을 복사하여 옮기고, 마이그레이션 중에 들어오는 데이터 변경사항은 Transaction log를 통해서 옮기게 된다



1. **target database 생성**: target database는 source database의 엔진, 스키마와 같을 수 도 있고 다를 수도 있다.
2. **마이그레이션 기법을 이용하여 마이그레이션 로직을 정의한다**: 데이터베이스가 제공하는 기능일수도 있고, [ Striim](https://www.striim.com/) or [Fivetran](https://fivetran.com/) 과 같은 독립된 마이그레이션 시스템일 수도 있다.
3. **초기 로드를 수행한다**: transactionally consistent한 source database의 snapshot을 target database로 복사한다.
4. **CDC를 통한 continuous 마이그레이션 수행하기**: 3을 수행한 시점 이후로 발생하는 변경 사항등은 transaction log를 통해 commit 완료된 transaction을 순서대로 target database에 적용시켜준다. (Change Data Capture)
5. **완전히 전환하기**: 클라이언트 요청이 target database로 전환될 시점에, source database의 write access를 멈추고, 남아있는 commited trasaction을 target database에 적용시켜준다. 그 후에 클라이언트를 target database로 연결시켜준다.



이렇게 마이그레이션을 수행하는 동안, 클라이언트는 제한없에 서비스를 이용할 수 있게 된다. (완전히 전환할때의 짧은 시간 동안만 접근이 제한된다)

또한, 마이그레이션을 수행하기 위해 클라이언트의 코드가 바뀔 필요가 없다.



## Dual-write 방법

<img src="https://miro.medium.com/max/373/1*8_BnqnWRKin_DTB11eW77A.png" style="zoom:150%;" />

마이그레이션이 수행되는 동안 데이터 유실이 없도록, 클라이언트는 source database와 target database에 모두 데이터를 읽고 쓰게 된다.

1. "new" database schema 생성

2. "new"에 write하는 새로운 DAO 추가("old"와 "new"에 모두 write할 수 있도록)하여 배포

   - 이 때, multi-state feature toggle을 마련하여(구현은 알아서) 재배포없이, 읽기 쓰기 동작을 바꿀수있게 준비

3. "new"에 write 하기 시작 

   - primary한 database는 "old"를 쓴다 (순서도 참고)

   ![](https://static.wixstatic.com/media/66bc35_bdad48ef2b5b48d182dbc54a6803868b~mv2.jpg/v1/fill/w_428,h_610,al_c,lg_1,q_90/66bc35_bdad48ef2b5b48d182dbc54a6803868b~mv2.webp)

   <center>출처: https://www.aviransplace.com/post/safe-database-migration-pattern-without-downtime-1#ixzz3vsEunxmA</center>

4. 두 개의 database에서 모두 읽을 수 있도록 활성화
5. "new" 을 primary database로 바꾸기
   -  multi state feature toggle을 설정해 3의 순서도에서 "new"에 먼저 write하도록 변경
6. "old" database의 쓰기 중단
7. "old" 의 데이터 ("new"엔 없는)를 "new"로 migrate
8. 어플리케이션에서 "old"의 DAO 제거



위의 CDC 방법에 비해서 클라이언트(여기선 어플리케이션) 코드가 변경되어야 하고, 배포도 두번이나 이루어져야 해서 번거롭다.

그리고 다양한 케이스에서 source와 target database의 state가 다르기 때문에 dual-write가 잘 이루어지지 않을 수도 있다.

(foriegn key 를 가진 row 삽입, update나 delete의 경우 source엔 있는 row이지만 target엔 없음)



내가 참고한 글에선, dual write 방식의 마이그레이션에 대해 수많은 문제점을 지적하고 있으며 고려해야할 점이 정말 많아 보인다.



- 참고:
  - <https://medium.com/google-cloud/online-database-migration-by-dual-write-this-is-not-for-everyone-cb4307118f4b>
  - <https://www.aviransplace.com/post/safe-database-migration-pattern-without-downtime-1#ixzz3vsEunxmA>
