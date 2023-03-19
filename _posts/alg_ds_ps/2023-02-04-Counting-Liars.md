---
layout: single
title:  "[USACO] Counting Liars (Bronze)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - alg_ds_ps
use_math: true
---

문제: [Counting Liars (Bronze)](http://usaco.org/index.php?page=viewproblem2&cpid=1228)

Bessie가 일직선상 어딘가에 위치하고 있고 $N (1 \leq N \leq 1000)$마리의 다른 소들은 진실일수도 거짓일수도 있는 Bessie의 위치에 대한 정보를 각각 갖고 있다. 

$i$번째 소는 L(less or equal) 또는 G(Greater or equal)과 $p_{i} (0 \leq p_{i} \leq 10^9)$ 정보를 갖고 있다. 예를 들어 10번째 소가 가진 정보가 L과 200이면 Bessie가 일직선상에서 200이하 어딘가에 있다고 주장(claim)하는 것이다 (물론 진실일수도 거짓일 수도 있다.)

Bessie가 일직선상 어딘가에 존재하고 이 위치를 기반으로 N마리 소들 중 거짓말을 하고있는 소의 마리수를 구했을때 이 값이 최소라면 이 값은 무엇을까?

### Approach 1 (Naive)
문제를 풀기 위해 가장 직관적인 접근은 Bessie의 위치를 0부터 $10^9$까지 1씩 증가시켜나가며 거짓말하는 소의 마리수를 구하고 이 중 최소값을 취하면 된다.

하지만 이 방식은 제한시간내에 답을 내놓지 못한다. Bessie의 각 위치마다 1000마리의 주장을 확인해야 하기때문이다. 즉, 시간복잡도는 $O(10^9 * N)$이고 $N$이 최대 1000인 것을 생각하면 $O(10^9 * 10^3) \approx O(10^{12})$이기 때문이다. 

### Approach 2 (Viable)
Bessie 위치를 0부터 $10^9$까지 1씩 증가시켜 가정하는 방식대신 주어진 $p_{i}$값들을 활용하면 어떨까? 만약 이게 된다면 우리가 가정해봐야하는 Bessie의 위치는 최대 1000밖에되지 않게 된다. 총 시간복잡도는 $O(N^2)$이다. 실제 최대 N값을 대입하면 $O(1000^2) \approx O(1e6)$ 정도밖에 안된다. 즉, 제한시간내에 문제의 해답을 찾을 수 있다.

그렇다면 무턱대고 주어진 $p_{i}$값을 Bessie의 위치를 가정하기 위해 사용해도 문제가 없을까? 

결론부터 말하자면 거짓말 하는 소의 마리수를 최소화하는게 목표이고 L과 G가 부등식을 의미하기에 문제가 되지 않는다. 


![counting liars img 1](/assets/image/alg_ds_ps/counting_liars/counting_liars_img_1.png)
예를들어 위 그림에서처럼 어떤 소가 Bessie의 방향과 위치가 각각 G와 100이라고 주장했다고 해보자. 이는 Bessie의 위치가 100과 같거나 크다고 주장하는 것이다. 즉, Bessie의 위치가 100이라면 진실을 말하게 되는 것이므로 거짓 주장의 갯수 증가에 기여하지 않는다. 

#### Implementation 
위의 insight를 기반으로 아래와 같이 간단하게 구현할 수 있다.

```cpp
// Counting Liars
// http://usaco.org/index.php?page=viewproblem2&cpid=1228

#include <bits/stdc++.h>

using namespace std;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N;
  cin >> N;
  vector<pair<char, int>> cows(N);
  for (int i = 0; i < N; ++i) {
    cin >> cows[i].first >> cows[i].second;
  }

  int ans = 1e9;
  for (int i = 0; i < N; ++i) {
    int cnt = 0;
    for (int j = 0; j < N; ++j) {
      if (i == j) continue; // not necessary but is there for exactness
      if (cows[j].first == 'G') {
        if (cows[i].second < cows[j].second) cnt++;
      } else {
        if (cows[i].second > cows[j].second) cnt++;
      }
    }
    ans = min(ans, cnt);
  }

  cout << ans << "\n";

  return 0;
}
```

