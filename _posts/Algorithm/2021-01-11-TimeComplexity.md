---
layout: post
title:  "시간복잡도 표기법"
excerpt: ""

categories:
  - Algorithm
tags:
  - [Algorithm]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-12
last_modified_at: 2021-01-12
---
## 시간복잡도 표기법을 간단히 정리해보았다.

- Big-O Notation (빅 오 표기법)

  - 정의

    - ```|f(x)| <= C|g(x)| (for all x > N)```

    - 어떤 지s점 N에 대해서 N보다 큰 모든 x에 대해서 `|`f(x)`|` <= C`|`g(x)`|` 인 N과 C가 존재하면

      f(x) = O(g(x)) 라 표기 가능하다.

  - 최악의 경우에도 이정도 성능(g(x)은 보장된다는 의미

<br>

- Big-Ω Notation(빅 오메가 표기법)

  - 정의

    - ```|f(x)| >= C|g(x)| (for all x > N)``` 

    - 어떤 지점 N에 대해서 N보다 큰 모든 x에 대해서 `|`f(x)`|` >= C`|`g(x)`|` 인 N과 C가 존재하면

      f(x) = Ω(g(x)) 라 표기 가능하다.

  - 최상의 경우에도 이정도 성능(g(x)보다 같거나 느리다는 의미

<br>

- Big-θ Notation(빅 세타 표기법)
  - 정의
    - f(x) = O(g(x))와 f(x) = Ω(g(x))가 모두 참일 때 f(x) = θ(g(x))
  - 평균적으로 이정도 성능이 나온다는 의미