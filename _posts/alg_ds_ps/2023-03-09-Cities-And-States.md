---
layout: single
title:  "[USACO] Cities and States (Silver)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - alg_ds_ps
use_math: true
---

문제: [Cities and States (Silver)](http://www.usaco.org/index.php?page=viewproblem2&cpid=667)

$N (1 \leq N \leq 200000)$개의 city와 state pair가 주어진다. 

어떠한 순서쌍들은 아래의 예시에서 처럼 특정조건을 만족하면 special pair라고 부른다.

ex)
![cities and states silver img 1](/assets/image/alg_ds_ps/cities_and_states_silver/cities_and_states_silver_img_1.png)


### Approach 1 (Naive)

단순하게 2중 for loop으로 city, state pair을 매번 2개씩 골라 가능한 모든 경우를 검사한다.

```cpp

for (int i = 0; i < N - 1; ++i) {
  for (int j = i + 1; j < N; ++j) {
    // check if they form a special pair
  }
}
```
하지만 이 접근은 입력의 크기가 최대 20만임을 고려하면 시간복잡도가 $O(N^2)$이기 때문에 실제로 사용할 수 없다.

### Approach 2

그렇다면 어떠한 city, state pair가 주어졌을때 special pair가 되기위한 조건을 갖춘 다른 pair를 좀 더 효율적으로 찾는 방법이 없을까?

어떤 city, state pair가 주어지면 **주어진** city의 앞의 두글자와 일치하는 state중에서 **주어진** state와 일치하는 앞 두글자를 가진 city의 갯수를 효율적으로 찾으면 된다.

이를 문제에서 주어진 입력을 각 state에 존재하는 city(앞 두글자만 고려)들의 갯수를 카운트하는 전처리 과정을 거친다.

예를 들어 주어진 입력이 아래와 같다면 
ex)
MIAMI1 FL

MIAMI2 FL

"FL" state에 "MI"로 시작하는 도시의 개수는 2개이다.

이 전처리 과정은 map 자료구조를 통해 구현하면 효율적으로 search를 할 수 있다. 

C++의 STL에서 제공하는 std::map의 경우 RB tree기반이기 때문에 search의 시간복잡도가 $O(N \log_2 {N})$이고 std::unordered_map의 경우 Hash기반이기 때문에 search 시간복잡도는 $O(\alpha \cdot 1 )$이나 constant factor인 $\alpha$가 매우 클수도 있어서 상황에따라 Time limit exceed verdict를 받을 수 있기에 사용을 추천하지는 않는다고 한다.

전처리 과정을 마친 후 각 pair에 대해 special pair를 만들어내는 개수를 각각 누계한다. 

정답은 순서가 중요하지 않기 때문에 바로 앞에서 누계한 값을 반으로 나눈 값이다. 

시간복잡도는 $O(N \log_2(N) \log_2(N))$이므로 $N=200000$인 경우에도 $O(200000 \log_2({200000}) \log_2({200000})) \approx O(61952000)$ 정도로 제한시간내에 충분히 문제를 풀어낼 수 있다.

#### Implementation
```cpp
// Cities and States (Silver)
// http://www.usaco.org/index.php?page=viewproblem2&cpid=667

#include <bits/stdc++.h>

using namespace std;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N;
  cin >> N;

  vector<pair<string, string>> v(N);
  for (int i = 0; i < N; ++i) {
    cin >> v[i].first >> v[i].second;
  }

  map<string, map<string, int>> cnt;
  for (int i = 0; i < N; ++i) {
    cnt[v[i].second][v[i].first.substr(0, 2)]++;
  }

  long long ans = 0;
  for (int i = 0; i < N; ++i) {
    if (v[i].first.substr(0, 2) == v[i].second) continue;
    ans += (long long)cnt[v[i].first.substr(0, 2)][v[i].second];
  }

  cout << ans / 2 << "\n";

  return 0;
}
```

