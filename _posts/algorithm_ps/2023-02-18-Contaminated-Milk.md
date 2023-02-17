---
layout: single
title:  "[USACO] Contaminated Milk (Bronze)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - algorithm_ps
use_math: true
---

문제: [Contaminated Milk (Bronze)](http://www.usaco.org/index.php?page=viewproblem2&cpid=569)

총 $M$ 종류의 다른 우유가 있고 이 중 하나가 상한 우유이다. 
만약 어떤 사람이 언제 어떤 종류의 우유를 마셨는지와 어떤 사람이 언제부터 아프기 시작했는지에 대한 정보가 주어지면 상한 우유를 마시고 아플 사람들을 위해 구비해야할 약의 최대 개수를 구할 수 있을까?

### Approach 1
우리는 먼아픈 사람의 정보를 통해 상한 우유의 가능성이 있는 후보군들을 추려낼수있다.

주어진 테스트케이스를 이해를 돕기위해 활용해보자.

![contamilted milk img 1](/assets/image/algorithm_ps/contaminated_milk_bronze/contaminated_milk_bronze_img_1.png)

주어진 정보에서는 총 2명이 결국 아프게 되었고 이는 빨간색 화살표로 표시하였다.
먼저 **Person 1**이 시간 $t=3$에서 아프게 되었는데 이는 Person 1을 아프게 할 수 있는 상한 우유들은 아프기 이전에 마신 1, 2, 4번 우유라는 것을 알 수 있다. 
다음으로 **Person 2**가 시간 $t=8$부터 아프기 시작하였다. 여기서 Person 2는 1번이나 2번 우유를 마시고 아프기 시작했음을 알 수 있다.

여기서 잊지말아야할 문제에서 주어진 중요한 단서중에 하나는 상한 우유는 단 한개뿐이라는 것이다. 여기까지 놓고보면 1, 2, 4번 우유가 상한 우유라고 짐작할 수 있다. 하지만 사실 4번 우유는 상한 우유 후보군에 들수가 없다. 4번 우유가 상한 우유라면 4번 우유를 마시지 않은 **Person 2**는 아플수가 없기 때문이다. 즉, 상한 우유 후보들은 1번과 2번 우유뿐이다. (아프다고 주어진 사람들이 아프기 시작한 시점 이전에 마셨던 우유 집합들의 교집합을 찾으면 된다.)

그렇다면 1번이나 2번 우유 중 하나가 상한 우유라고 했을때 이미 아픈 사람들과 가까운 미래에 아플 수도 있는 사람들을 치료하기 위해 준비해야될 약은 최대 몇개인가? 

여기서 부터는 상한 우유 후보군에 있는 우유들을 각각 상한 우유라고 가정하고 시뮬레이션을 돌려보면 된다.  

예를 들어 1번 우유가 상했다고 가정하고 시뮬레이션을 돌려보면 **Person 1, 2, 3**이 아프게 될 것이다 (구비해야되는 약의 개수는 3개). 반대로 2번 우유가 상했다고 가정하고 시물레이션을 돌려보면 **Person 1, 2**만 아플것이다 (구비해야되는 약의 개수는 2개). 우리는 최대로 구비해야할 약의 개수를 구해야하기때문에 정답은 3개이다.

### Implementation
```cpp
// Contaminated Milk
// http://www.usaco.org/index.php?page=viewproblem2&cpid=569

#include <bits/stdc++.h>

using namespace std;

struct Info {
  int p, m, t;
};

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N, M, D, S;
  cin >> N >> M >> D >> S;

  vector<Info> v(D);
  for (int i = 0; i < D; ++i) {
    cin >> v[i].p >> v[i].m >> v[i].t;
  }

  // Compute possible candidates for the bad milk
  bool first = true;
  bool intersect[51]{};  // since M <= 50
  for (int i = 0; i < S; ++i) {
    int p, t;
    cin >> p >> t;

    bool cand[51]{};
    for (int j = 0; j < D; ++j) {
      if (v[j].p != p) continue;
      if (v[j].t >= t) continue;
      cand[v[j].m] = true;
    }

    if (first) {
      for (int j = 1; j <= M; ++j) {
        intersect[j] = cand[j];
      }
      first = false;
    } else {
      for (int j = 1; j <= M; ++j) {
        intersect[j] &= cand[j];
      }
    }
  }

  // Assume each candidate as the bad one and compute possible number of the
  // sick
  int ans = 0;
  for (int i = 1; i <= M; ++i) {
    if (intersect[i] == false) continue;

    bool who_drank[51]{};
    for (int j = 0; j < D; ++j) {
      if (v[j].m == i) {
        who_drank[v[j].p] = true;
      }
    }
    int cnt = 0;
    for (int j = 1; j <= N; ++j) {
      if (who_drank[j]) ++cnt;
    }
    ans = max(ans, cnt);
  }

  cout << ans << "\n";

  return 0;
}
```