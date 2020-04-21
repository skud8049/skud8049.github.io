---
title: "[C++] 자료구조 힙(heap)의 구조와 구현"
excerpt: "자료구조 힙(heap)의 구조와 구현"

toc: true
toc_sticky: true

categories:
  - 자료구조
tags:
  - heap
last_modified_at: 2020-04-21T11:40:00
---

# 1. 구조

- 완전이진트리 구조 형태로 루트값이 최대 또는 최소 값이 되는 구조
- 여러개의 데이터 중 `최대`, `최소`를 빠르게 찾을 수 있다.
- 최대힙과 최소힙 두가지 종류가 있다.

<table>
	<tr>
		<td><img src=https://github.com/skud8049/skud8049.github.io/blob/master/assets/images/max_heap.png?raw=true)></td>
		<td><img src=https://github.com/skud8049/skud8049.github.io/blob/master/assets/images/min_heap.png?raw=true></td>
	</tr>
	<tr>
		<td><center>[최대 힙]</center></td>
		<td><center>[최소 힙]</center></td>
	</tr>
	<tr>
		<td><center>Root의 값이 최대가 된다</center></td>
		<td><center>Root의 값이 최소가 된다</center></td>
	</tr>
</table>

