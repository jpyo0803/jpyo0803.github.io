---
layout: single
title:  "[USACO] Load Balancing (Silver)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - algorithm_ps
use_math: true
---

문제: [Load Balancing (Silver)](http://www.usaco.org/index.php?page=viewproblem2&cpid=619)

[이전 포스트](https://jpyo0803.github.io/algorithm_ps/Load-Balancing-Bronze/)에 [Load balancing (Bronze 레벨)](http://www.usaco.org/index.php?page=viewproblem2&cpid=617)에서는 $N (1\leq N \leq 100)$이므로 시간복잡도가 $O(N^3)$임에도 불구하고 제한시간내에 충분히 해결가능했다.

하지만 Silver 레벨에서는 $N (1 \leq N \leq 1000)$이므로 이전의 알고리즘으로는 **TLE**로 인해 실패하게 된다.

### Approach 1
이전의 알고리즘에서 시간복잡도를 더 줄일 수 있는 방법은 없을까?

기존 알고리즘의 시간복잡도가 $O(N^3)$이었던 이유는 총 3가지였다. 

**Part 1**: 각 인접한 소들의 x축 위치들의 사이값들의 개수는 대략적으로 $N$개정도가 된다. 

**Part 2**: 마찬가지로, 각 인접한 소들의 y축 위치들의 사이값들의 개수는 대략적으로 $N$개정도가 된다. 

**Part 3**: $x=a$와 $y=b$가 정해지면 $N$마리의 소들을 매번 순회하며 4개의 지역으로 나눈다. 각 소를 한번씩 고려해야하므로 총 $N$번의 오퍼레이션이 든다.

즉, 위의 3가지 요소들을 모두 고려하면 시간복잡도는 $O(N^3)$이 나오게 된다.

사실 **Part 1**과 **Part 2**에서 더 줄일 수 있는지는 모르겠으나 **Part 3**는 각 케이스마다 $x=a$와 $y=b$가 정해질때마다 매번 새로 수행할 필요는 없어보인다. 


![load balancing img 1](/assets/image/algorithm_ps/load_balancing_silver/load_balancing_silver_img_1.png)

예를들어 위에 그림에서처럼 $x=a$가 고정된 상태에서 $y=b$가 다음 후보위치로 이동했다고 해보자. 

![load balancing img 2](/assets/image/algorithm_ps/load_balancing_silver/load_balancing_silver_img_2.png)
그럼 위에서 화살표로 표시된 소가 속한 그룹이 **I**에서 **IV**로 변경된 것을 확인 할 수 있다. 

즉, 어떤 소가 현재 $x=a$의 값을 기준으로 왼쪽에 있으면 **II**에서 **III**으로 이동시키고 오른쪽에 있으면 **I**에서 **IV**로 이동시키면 된다.

이 아이디어를 사용해 $y=b$가 다음 위치로 이동할때마다 $O(1)$의 연산만 추가적으로 해주면된다.

물론 상황에 따라 소들이 같은 $y$값을 가질 수 있어 모든 소들이 같은 $y$값을 갖는 경우에는 한번 $y=b$ 이동시에 최대 $N$번의 소들을 처리해야되는 경우도 있다. 하지만 각 $x=a$마다 모든 $N$마리의 소들을 한번씩은 순회해야하기 때문에 $y=b$가 이동할때 몇 마리의 소를 처리하는지는 알고리즘의 시간복잡도에 영향을 주지 않는다. 

그래서 최대 크기 $N$을 갖는 집합 $X=\{a_{1}, a_{2}, ...\}$의 각 원소에 대해 $N$번씩 알고리즘을 수행해야 하므로 총 시간복잡도는 $O(N^2)$가 되며 이는 주어진 $N (1 \leq N \leq 1000)$에 대해 충분히 효율적인 알고리즘이다.

#### Implementation
```cpp
// Load Balancing (Silver)
// http://www.usaco.org/index.php?page=viewproblem2&cpid=619

#include <bits/stdc++.h>

using namespace std;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N;
  cin >> N;

  vector<pair<int, int>> cows(N);
  cows.emplace_back(1e9, 1e9);

  for (int i = 0; i < cows.size(); ++i) {
    cin >> cows[i].first >> cows[i].second;
  }

  vector<int> xind, yind;
  for (int i = 0; i < cows.size(); ++i) {
    xind.push_back(i);
    yind.push_back(i);
  }

  sort(xind.begin(), xind.end(),
       [&](int a, int b) { return cows[a].first < cows[b].first; });
  sort(yind.begin(), yind.end(),
       [&](int a, int b) { return cows[a].second < cows[b].second; });

  int ans = 1e9;
  int i = 0;
  while (i < xind.size()) {
    int q1 = N - i, q2 = i, q3 = 0, q4 = 0;

    int j = 0;
    while (j < yind.size()) {
      while (j + 1 < yind.size() &&
             cows[yind[j]].second == cows[yind[j + 1]].second) {
        if (cows[yind[j]].first < cows[xind[i]].first) {
          --q2;
          ++q3;
        } else {
          --q1;
          ++q4;
        }
        ++j;
      }
      if (cows[yind[j]].first < cows[xind[i]].first) {
        --q2;
        ++q3;
      } else {
        --q1;
        ++q4;
      }
      ++j;

      ans = min(ans, max({q1, q2, q3, q4}));
    }

    while (i + 1 < xind.size() &&
           cows[xind[i]].first == cows[xind[i + 1]].first) {
      ++i;
    }
    ++i;
  }

  cout << ans << "\n";

  return 0;
}
```