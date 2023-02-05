---
layout: single
title:  "[USACO] Lifeguards (Bronze)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - algorithm_ps
use_math: true
---

문제: [Lifeguards (Bronze)](http://www.usaco.org/index.php?page=viewproblem2&cpid=784)

농부 존은 총 $N (1 \leq N \leq 100)$마리의 안전요원을 채용했는데 이들 중 하나를 해고해야 현재 예산을 맞출수 있다. 어떤 안전요원을 해고했을때 아직도 커버 가능한 최대 영역은 얼마인가를 구해야 한다.

### Approach 1
단순하게 1번부터 $N$번까지의 소들을 한마리씩 ($i$번째 소라고하자) 포함하지 않았을때 최대 커버 영역을 구하면 된다. 커버 가능한 영역은 $0 \leq t \leq 1000$ 이므로 단순하게 $i$번째 소를 제외한 각 소들이 커버하는 영역을 1차원 배열에 커버여부를 표시하는 과정을 거친뒤 최종적으로 커버된 영역의 개수를 세면 된다.
즉, 시간복잡도는 $O(1000*N^2)$이다. $N=100$이라고하면 $O(1e7)$. 제한시간내에 충분히 정답을 구해낼수 있다.

### Approach 2
커버된 영역의 개수를 좀 더 효율적으로 구하는 방법은 없을까?

1차원 Line Sweep 알고리즘을 활용하면 시간복잡도를 $O(N^2+NlgN) \approx O(N^2)$까지도 줄일 수 있다.

1차원 Line Sweep 알고리즘을 우리의 문제에 적용하기 위해서는 커버 영역의 시작점을 기준으로 오름차순으로 정렬한다. 그리고 정렬된 순서로 앞에서부터 하나씩 처리한다.

#### Case 1 ($t_{c}<t_{b}\ \land\ t_{b} < t_{d}$)
![lifeguards img 1](/assets/image/algorithm_ps/lifeguards_bronze/lifeguards_img_1.png)
만약 위의 Case 1에서의 조건을 따른다면 구간 [t_{a},t_{b}]는 이미 처리되었고 구간 [t_{c},t_{b}]는 중복구간 [t_{a},t_{b}]와 중복되기 때문에 스킵한다. 그리고 구간 [t_{b},t_{d}]는 따로 더해준다. 즉, 앞에서부터 커버 영역을 구하면 

$coverage = [t_{a}, t_{b}] + [t_{b}, t_{d}]$

#### Case 2 ($t_{a}<t_{c}\ \land\ t_{d} < t_{b}$)
![lifeguards img 2](/assets/image/algorithm_ps/lifeguards_bronze/lifeguards_img_2.png)

단순하게 구간 $[t_{c},t_{d}]$는 무시된다.

$coverage = [t_{a}, t_{b}]$

#### Case 3 ($t_{b}<t_{c}$)
![lifeguards img 3](/assets/image/algorithm_ps/lifeguards_bronze/lifeguards_img_3.png)

이번에도 단순하다. 그냥 둘을 따로 계산하면 된다.

$coverage = [t_{a}, t_{b}] + [t_{c}, t_{d}]$

#### Implementation
Approach 1에서 제시된 아이디어에 Approach 2에서 제시된 좀 더 효율적인 커버 영역 집계 알고리즘을 적용하여 구현하면 아래이 구현 가능하다.

```cpp
// Lifeguards
// http://www.usaco.org/index.php?page=viewproblem2&cpid=784

#include <bits/stdc++.h>

using namespace std;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N;
  cin >> N;

  vector<pair<int, int>> v(N);
  for (int i = 0; i < N; ++i) {
    cin >> v[i].first >> v[i].second;
  }

  sort(v.begin(), v.end());  // sort it by earlier starting time

  int ans = 0;
  for (int i = 0; i < N; ++i) {  // 'i' to exclude
    int sum = 0;

    int last_ended = -1;
    for (int j = 0; j < N; ++j) {
      if (i == j) continue;
      if (last_ended > v[j].first) {
        if (last_ended < v[j].second) {
          sum += v[j].second - last_ended;
          last_ended = v[j].second;
        }
      } else {
        sum += v[j].second - v[j].first;
        last_ended = v[j].second;
      }
    }

    ans = max(ans, sum);
  }

  cout << ans << "\n";

  return 0;
}
```