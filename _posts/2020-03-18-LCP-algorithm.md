---
title: "SWEA 1257. K번째 문자열"
excerpt: "SWEA 1257. 문제 풀이"

toc: true
toc_sticky: true

categories:
  - 알고리즘
tags:
  - LCP
  
last_modified_at: 2020-03-18T03:53:00
---

아직 이해가 부족한 관계로 이해한 지식에 한해서 작성함.

# 문제출처

[{{post.title}}](https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV18KWf6ItECFAZN&categoryId=AV18KWf6ItECFAZN&categoryType=CODE "바로가기"){: target="_blank"}




# 문제풀이

부분 문자열들을 순서대로 나타내었을 때, K번째 문자열이 무엇인지 구하는 문제.
간단하게 생각하면 모든 부분 문자열을 나열 후 사전순에 맞게 정렬 후 K번째 문자를 구하는 간단한 문제.
하지만, 중복도 발생하기 때문에 중복된 문자를 제거해주는 작업까지 해주어야 한다.

<center>

||food|
|:-:|:-:|
|선택개수|가능한 문자열|
|1개|f, o, o, d|
|2개|fo, ~~fo~~, fd|
|3개|foo, fod, ~~fod~~|
|4개|food|

</center>

이와같은 방법은 정답을 찾을 수 있지만 퍼포먼스가 낮게 나온다. 따라서, 접미사(Suffix)를 이용한 방법을 통해 구하였다.






# 코드
