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

아직 이해가 부족한 관계로 원리보다는 풀이방법을 기준으로 작성하였습니다.

# 문제출처

[{{page.title}}](https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV18KWf6ItECFAZN&categoryId=AV18KWf6ItECFAZN&categoryType=CODE "바로가기"){: target="_blank"}




# 문제풀이

부분 문자열들을 순서대로 나타내었을 때, K번째 문자열이 무엇인지 구하는 문제.

간단하게 생각하면 선택 개수에 따라 생성되는 부분 문자열을 나열 후 사전순에 맞게 정렬 후 K번째 문자를 구하는 간단한 문제.

||food|
|:-:|:-:|
|선택개수|생성된 부분 문자열|
|1개|f, o, o, d|
|2개|fo, fd|
|3개|foo, fod|
|4개|food|

이와같은 방법은 정답을 찾을 수 있지만 모든 부분 문자열을 찾은 후 그에 대한 정렬까지 해주어야 하기 때문에 퍼포먼스가 낮게 나온다. 따라서, `접미사(Suffix)`를 이용한 방법을 통해 구하였다.

1. 먼저 문자열에 대한 접미사를 구하여 SA(Suffix Array)에 저장 후 사전순으로 정렬한다.

|SA|index||SA|index|
|--:|:-:|-|-:|:-:|
|food|0||d|3|
|ood|1||food|0|
|od|2||od|2|
|d|3||ood|1|

- index = index 위치부터 나타낸 접미사

이렇게 정리된 접미사를 앞에서 하나씩 잘라 나열하면 모든 부분문자열을 정렬한 것과 같다.

|index|SA|부분 문자열|
|-|-|:-|
|3|d|d|
|0|food|f|
|||fo|
|||foo|
|||food|
|2|od|o|
|||od|
|1|ood|o (중복발생)|
|||oo|
|||ood|

하지만 중복이 발생하기 때문에 LCP(Longest Common Prefix)를 이용하도록 한다.

2. 앞쪽의 문자열과 비교해서 같은 접두사의 최대 길이를 구한다.

|SA|index|LCP|
|-:|:-:|:-:|
|d|3|0|
|food|0|0|
|od|2|0|
|ood|1|1|

 - 3번줄의 `od`와 4번줄의 `ood`의 `o`가 앞쪽에 공통적으로 있다.
 - 이는 곧 1개의 중복된 문자열이 있다는 의미

따라서, 각 SA의 문자열이 나타낼 수 있는 부분 문자열의 개수는 이렇게 될 것이다.

    부분 문자열의 개수 = 입력 문자열의 길이 - index - LCP(=중복)

|SA|index|LCP|부분 문자열의 개수|
|-:|:-:|:-:|:-:|
|d|3|0|4-3-0 = 1|
|food|0|0|4-0-0 = 4|
|od|2|0|4-2-0 = 2|
|ood|1|1|4-1-1 = 2|

이제 위와 같은 정보를 가지고 K번째 문자열을 구할 수 있다.

# 코드

```c++

#include <iostream>
#include <string>
#include <string.h>
#include <algorithm>
using namespace std;

struct suffix {
	string st;
	int index;
};

bool compare(const suffix& a, const suffix& b) {
	return a.st < b.st;
}

int min(int a, int b) {
	if (a > b)return b;
	else return a;
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int test;
	cin >> test;
	for (int t = 1; t <= test; t++) {

		//입력부
		int find;
		string input;
		cin >> find >> input;

		//초기화부
		suffix SA[400];
		int LCP[400];
		memset(LCP, 0, sizeof(LCP[0]) * input.size());

		// suffix array는 입렵된 문자열 길이만큼 생성됨
		// SA에 suffix 문자열과 몇 번째 위치부터 시작된 suffix인지 저장한다
		for (int i = 0; i < input.size(); i++) {
			SA[i].st = input.substr(i, input.size());
			SA[i].index = i;
		}

		// SA에 저장된 문자열을 사전순으로 정렬
		sort(SA, SA + input.size(), compare);

		// 이전 순서의 문자열과 공통된 접두어의 최대길이를 구한다.
		for (int i = 1; i < input.size(); i++) {

			int min_size = min(SA[i].st.size(), SA[i - 1].st.size());

			for (int j = 0; j < min_size; j++) {

				if (SA[i].st[j] == SA[i - 1].st[j]) {
					LCP[i]++;
				}
				else break;
			}
		}

		int sum_cnt = 0;
		string result_st;

		for (int i = 0; i < input.size(); i++) {

			// SA[i]에서 만들수 있는 부분 문자열의 개수
			// LCP[i]개 만큼 중복되기 때문에 빼준다.
			int make_cnt = input.size() - SA[i].index - LCP[i];

			sum_cnt += make_cnt;

			if (find <= sum_cnt) {

				sum_cnt -= make_cnt;

				int cnt = find - sum_cnt + LCP[i];

				result_st = SA[i].st.substr(0, cnt);

				break;
			}
		}

		cout << "#" << t << " ";

		if (result_st.size() == 0) cout << "none" << "\n";
		else cout << result_st << "\n";
	}

	return 0;
}
```