---
layout: single
title:  "[CF] Jury Marks"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - algorithm_ps
use_math: true
---

문제: [Jury Marks (CF)](https://codeforces.com/contest/831/problem/C)

### Approach 1 (Naive)
이 문제를 풀기위한 기본적인 아이디어는 각 Jury들이 평가를 매기기전 가능한 최초 점수 후보군들을 추려내는 것이다. 

각 Jury는 $a_{1}, a_{2}, ..., a_{K}$ 점수들을 시간순서대로 반영해나간다.

즉, 최초 점수가 $x_{0}$라면 $a_{k}$번째 평가가 반영된 후의 점수인 $x_{k}$는 아래의 수식을 통해 표현할 수 있다. 

$x_k=x_{0} + \sum_{i=1}^{k} a_{i}$

Polycarp가 중간중간 기억하는 $b_{1}, b_{2}, ..., b_{N}\  (1\leq N \leq2000)$ 값들을 활용해 평가가 매겨지기전 최초 점수 후보군들을 생성해낼 수 있다. 

예를들어 어떤 점수 $b$를 $a_{k}$번째 점수가 반영된 후 보았다면 최초의 점수는 간단하게 아래의 식을 통해 구할 수 있다.

$x_{0}=b-\sum_{i=1}^{k}a_{i}$

위의 방식을 통해 어떤 점수 $b$를 각각 $a_{1}, a_{2}, ..., a_{K}$번째 평가가 매겨진 후 보았다고 가정하고 최초의 점수로 가능성있는 후보들을 구해낼 수 있다.

매번 $\sum_{i=1}^{k} a_{i}$를 구하는 것은 불필요하게 시간복잡도를 증가시킨다. 

**Prefix sum**을 사용하여 어떠한 지점에서 본 점수를 기반으로 최초 점수를 구하는 시간복잡도를 $O(N)$에서 $O(1)$로 줄여보자.

$ps_{k}=\sum_{i=1}^{k}a_{i}$ 이라고 정의하면

$x_{0} = b - ps_{k}$ 가 된다.

위의 과정을 모든 $b$에 대해 수행하면 최대로 가능한 최초 점수 후보군의 크기는 $O(N*K)$이다. $1 \leq N \leq K \leq 2000$임을 감안하면 실제 크기는 최대 $O(2000^2)$이다. 

이제부터는 최초 점수 후보군의 후보를 각각 시작 점수로 활용하여 Jury들의 평가를 시간순으로 적용하여 얻을 수 있는 점수 집합 $C$를 구한다.

만약 입력으로 받은 $B=\{b_{1}, b_{2}, ..., b_{N}\}$이 $B \subseteq C$라면 해당 후보는 feasible한 후보가 된다.

여기서 집합 $B$의 모든 원소가 $C$에 포함되어있는지 확인하기 위해 필요한 시간복잡도는 std::set을 사용하였을 경우 $O(N\log_{2}K)$이다. 

$1 \leq N \leq K \leq 2000$임을 고려하면 시간복잡도는 $O(2000\log_{2}2000)$이다. 

여기서 후보군에 포함된 모든 후보들 $O(2000^2)$ 테스트해야하는 것까지 생각하면 총 시간복잡도는 $O(N*K^2\log_{2}K) \approx O(2000^3\log_{2}2000)$이므로 제한시간내에 문제를 풀지 못하게 된다.

### Approach 2 
Approach 1의 문제는 후보군의 크기가 너무 크다는 것이었다. 

결론부터 말하자면 집합 $B=\{b_{1}, b_{2}, ..., b_{N}\}$에 포함된 모든 원소들 중 **하나만** 선택해서 후보군 생성시에 사용하면 된다. 

이게 되는 이유는 집합 $B$의 모든 원소들이 사용되야한다는 점이다.

즉, 어떤 원소 $b \in B$는 어떤 평가 $a \in A$뒤에 반드시 나타날 것이기에 하나의 원소 $b$만 사용해서 후보군들을 구해도 초기 점수로 가능성있는 모든 후보군을 추려낼 수 있다.  

반대로 만약 집합 $B$의 일부분만 사용해 정답을 구성해도 된다고하면 각 원소 $b$를 사용해 각 평가 $a$ 위치를 기준으로 초기 점수를 계산해 후보군을 생성해내야 할 것 같다. (사실 완전히 다른 문제가 되어버리는 것 같다...)

위의 아이디어가 맞는 것은 알겠는데 설명하려니 아리송한 부분이 있다. 

어쨌든 위 아이디어를 통해 총 시간복잡도를 $O(K^2\log_{2}K)$로 줄일 수 있다.

#### Implementation
```cpp
#include <bits/stdc++.h>

using namespace std;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int K, N;
  cin >> K >> N;

  vector<int> ps(K), b(N);
  for (int i = 0; i < K; ++i) {
    cin >> ps[i];
    if (i > 0) {
      ps[i] += ps[i - 1];
    }
  }

  for (int i = 0; i < N; ++i) {
    cin >> b[i];
  }

  vector<int> cand;
  for (int i = 0; i < K; ++i) {
    cand.push_back(b[0] - ps[i]);
  }

  sort(cand.begin(), cand.end());
  cand.erase(unique(cand.begin(), cand.end()), cand.end());

  int ans = 0;

  for (auto e : cand) {
    set<int> c;
    for (int i = 0; i < K; ++i) {
      c.insert(e + ps[i]);
    }
    bool ok = true;
    for (int i = 0; i < N; ++i) {
      auto it = c.find(b[i]);
      if (it == c.end()) {
        ok = false;
        break;
      }
    }
    if (ok) ++ans;
  }

  cout << ans << "\n";

  return 0;
}

```
