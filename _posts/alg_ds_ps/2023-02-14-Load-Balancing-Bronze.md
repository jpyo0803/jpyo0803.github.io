---
layout: single
title:  "[USACO] Load Balancing (Bronze)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - alg_ds_ps
use_math: true
---

문제: [Load Balancing (Bronze)](http://www.usaco.org/index.php?page=viewproblem2&cpid=617)

이 문제는 $x,y$ 평면상에 존재하는 소들을 각각 $x$축과 $y$축에 평행한 끝없이 긴 직선 울타리 $x=a$와 $y=b$를 사용해 4개의 공간으로 각각 분할했을때 하나의 공간에 가능한 존재하는 소의 최소 마리수를 구하는 것이다. 즉, 어떤식으로 $x$축 평행한 직선 울타리(빨간색) $y$축과 평행한 직선 울타리(파란색)를 설치해야 4분할된 공간들(I, II, III, IV)에 각각 위치한 소들의 개수들 중 최대값을 최소화 할 수 있을까?

### How to count the number of cows in an individual partition(I, II, III, or IV)

문제에서 요구하는 답을 구하기 위해서는 울타리가 이미 설치된 상태에서 각 파티션에 존재하는 소의 마리수를 셀수있어야한다. 세는 방법은 간단하다.

![load balancing img 1](/assets/image/alg_ds_ps/load_balancing_bronze/load_balancing_img_1.png)

먼저 어떤 소의 위치를 $P(x,y)$라고 하자.

그렇다면 각 파티션에 존재하는 소의 마리수는 아래의 조건을 만족하는 소들의 수를 세면된다.

**Partition I**: $x>a \land y>b$

**Partition II**: $x<a \land y>b$

**Partition III**: $x<a \land y<b$

**Partition IV**: $x>a \land y<b$

문제에서 소들의 위치값 $x,y$는 항상 홀수값을 갖고 직선 울타리의 값 $a,b$는 항상 짝수임을 보장하므로 소의 좌표가 울타리 위에 겹치는 경우는 배제해도 된다.

각 파티션에 위치하는 소들의 마리수를 각각 구하기 위해 모든 소 $N$마리를 검사해봐야하므로 시간복잡도는 $O(N)$과 같다.

그렇다면 이제 직선 울타리의 위치들을 각각 선정해야한다. 

### Approach 1 (Naive)
울타리들의 위치를 선정하는 가장 직관적이고 쉬운 방법은 각각 울타리가 위치가능한 경우의 수를 모두 검사해보는 것이다. 

$B={1, 3, 5, ...}$

라고했을때 만약 $B$의 최대값이 100인 경우에는 

$B={1, 3, 5, ..., 99}$

$x=a, y=b$라고 했을때 

```cpp
for (int a = 1; a <= 99; a += 2) {
  for (int b = 1; b <= 99; b += 2) {
    // check
  }
}
```

중첩 for 문만 고려했을때 시간복잡도는 $O(B^2) \approx O(50^2)$ 정도이고 실제 내부에서 수행되는 알고리즘까지 고려했을때 시간복잡도는 $O(N*B^2) \approx O(100*50^2) \approx O(250000)$밖에 안되 손쉽게 Testcase 5까지는 통과 가능하다.

하지만 그 외에 Testcase에서는 $B \leq 1,000,000$이므로 시간복잡도는 $O(100*500000^2) \approx O(25,000,000,000,000)$정도에 현실적으로 사용 할수 없는 알고리즘이 된다.

### Approach 2 
그렇다면 어떻게 모든 순서쌍 ($x=a,y=b$)가 될수있는 경우의수를 효과적으로 줄일수있을까? 

상황을 간단하게 하기 위해 $x=a$으로 표현되는 직선 울타리만 고려해보자.

![load balancing img 2](/assets/image/alg_ds_ps/load_balancing_bronze/load_balancing_img_2.png)
위에서 그림은 Approach 1에서의 방식을 그대로 보여준다. 소 A와 소 B의 사이에서 직선 울타리가 가질 수 있는 모든 경우를 검사한다. 굳이 다 검사할 필요가 있을까?

![load balancing img 3](/assets/image/alg_ds_ps/load_balancing_bronze/load_balancing_img_3.png)
모든 경우를 다 검사하는 것은 시간낭비이다. 소A와 소B 사이의 $x=a$값하나만 검사해보면 된다. (여기서는 평균값을 사용했으나 굳이 그럴필요는 없다.)

그렇다면 소의 마리수는 $N$은 최대 100이고 100마리로 만들수 있는 사이값들은 총 99개가 가능하다. 이를 $x,y$축 모두에 대해 확장시키면 시간복잡되는 $O(N^2)$이된다. 검사 알고리즘까지 포함하면 $O(N^3)$이다. 즉, 제한시간내에 문제를 충분히 풀수있는 알고리즘이다.

### 특별히 주의해야되는 케이스
소가 한마리만 존재할때 위의 알고리즘은 처리하지 못한다. 이를 핸들링하기 위해 간단하게 가장자리에 있는 소보다 더 바깥쪽에 직선 울타리가 존재하는 케이스도 추가해주자.

### Implementation
```cpp
// Load Balancing (Bronze)
// http://www.usaco.org/index.php?page=viewproblem2&cpid=617

#include <bits/stdc++.h>

using namespace std;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N, B;
  cin >> N >> B;

  vector<pair<int, int>> cows(N);
  vector<int> xpos, ypos;
  for (int i = 0; i < N; ++i) {
    cin >> cows[i].first >> cows[i].second;
    xpos.push_back(cows[i].first);
    ypos.push_back(cows[i].second);
  }

  sort(xpos.begin(), xpos.end());
  sort(ypos.begin(), ypos.end());

  vector<int> x_cand, y_cand;
  x_cand.push_back(xpos[0] - 1);
  y_cand.push_back(ypos[0] - 1);

  for (int i = 0; i < xpos.size() - 1; ++i) {  // interpolation
    x_cand.push_back((xpos[i] + xpos[i + 1]) / 2);
    y_cand.push_back((ypos[i] + ypos[i + 1]) / 2);
  }

  x_cand.push_back(xpos.back() + 1);
  y_cand.push_back(ypos.back() - 1);

  int ans = 1e9;
  for (auto a : x_cand) {    // x = a
    for (auto b : y_cand) {  // y = b
      int q1 = 0, q2 = 0, q3 = 0,
          q4 = 0;  // quadrant from 1 ~ 4
      for (auto& [x, y] : cows) {
        if (x > a && y > b)
          q1++;
        else if (x < a && y > b)
          q2++;
        else if (x < a && y < b)
          q3++;
        else
          q4++;
      }
      ans = min(ans, max({q1, q2, q3, q4}));
    }
  }
  cout << ans << "\n";

  return 0;
}
```

