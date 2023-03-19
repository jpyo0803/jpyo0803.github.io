---
layout: single
title:  "[USACO] Sleepy Cow Herding (Bronze)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - alg_ds_ps
use_math: true
---

문제: [Sleepy Cow Herding (Bronze)](http://www.usaco.org/index.php?page=viewproblem2&cpid=915)

이 문제를 풀기위해 정형화되어있는 알고리즘은 없다. 보통 알려진 알고리즘이 없는 문제들을 **AD-Hoc** 문제라고 한다.

이 문제에서 모든 소들이 인접하게 위치하기 위해 필요한 **최소 / 최대** 이동횟수를 구해야 한다. 

**최소 / 최대**를 구하는 방법은 각각 다르다. 먼저 최소 이동 횟수를 구해보자.

### 최소 이동 횟수 구하기
각 상황별로 소들이 인접하기 위치하기 위해 필요한 최소 이동횟수를 직접 구해보았다.

###### Case 1 (소들이 이미 인접한 상황)
![sleepy cow herding bronze img 1](/assets/image/alg_ds_ps/sleepy_cow_herding_bronze/sleepy_cow_herding_bronze_img_1.png)
위의 상황을보면 어떤 소도 더 이상 이동할 필요가 없음을 확인 할 수 있다.

즉 위의 상황에서 최소 이동 횟수는 **0회**이다.

##### Case 2 (소들 사이의 거리가 1이 존재할때)
![sleepy cow herding bronze img 2](/assets/image/alg_ds_ps/sleepy_cow_herding_bronze/sleepy_cow_herding_bronze_img_2.png)

위의 상황들을 예시로 보면 소들 사이의 거리가 1일때가 있는 경우를 보여준다. 

맨 위의 상황은 가장 왼쪽의 소가 중간과 오른쪽 소들 사이에 위치하게 하면 된다. 

중간의 상황은 가장 왼쪽이나 오른쪽 소를 하나 골라 나머지 소들 사이에 위치시키면 된다.

마지막 상황은 가장 왼쪽 소를 Case 2의 첫번째 상황에서처럼 중간과 오른쪽 소 사이에 위치시키면 된다.

위의 모든 상황에서 필요한 최소 이동 횟수는 **1회**이다.

##### Case 3 (그 외의 상황)
![sleepy cow herding bronze img 3](/assets/image/alg_ds_ps/sleepy_cow_herding_bronze/sleepy_cow_herding_bronze_img_3.png)

그 외의 상황은 가장자리에 있는 소를 적절하게 움직여 **Case 2**의 상황으로 만들어준다. 

이 과정에서 Case 2의 상황을 만들기 위해 한번의 이동 횟수가 든다. 

그 다음 Case 2 상황에서 이동을 완료하기 위해 1번의 이동이 필요한 것을 우리는 이미 알고 있다.

즉, **2번**의 이동만 있으면 된다.

**결론적으로, 최소 이동 횟수는 상황에 따라 0번에서 2번의 이동만 필요한다.**

### 최대 이동 횟수 구하기
소들을 최대한 많이 이동시켜 인접하게 만드는 방법은 가장자리에 있는 두 마리의 소들중 한마리를 골라 고정시키고 나머지 소들을 번갈아가며 한칸씩 고정되어있는 소쪽으로 점진적으로 이동시킨다. 

![sleepy cow herding bronze img 4](/assets/image/alg_ds_ps/sleepy_cow_herding_bronze/sleepy_cow_herding_bronze_img_4.png)

위의 예시에서는 오른쪽 소를 고정시키고 왼쪽과 중간소를 번갈아가며 이동시켜 결국에는 세 마리 소들이 인접하게 하였다. 이 과정중에 필요한 이동 횟수는 $d2$번이다. 

즉 최대 이동 횟수는 중간 소를 기준으로 했을때 거리가 먼쪽의 소를 고정시키고 나머지 소들을 위의 아이디어를 따라 움직이면 된다. 

즉, 

$moves_{max} = max(d1, d2)$

#### Implementation
```cpp
// Sleepy Cow Herding
// http://www.usaco.org/index.php?page=viewproblem2&cpid=915

#include <bits/stdc++.h>

using namespace std;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  vector<int> v(3);
  for (int i = 0; i < 3; ++i) {
    cin >> v[i];
  }

  sort(v.begin(), v.end());

  int d1 = v[1] - v[0];
  int d2 = v[2] - v[1];

  int min_ans, max_ans;

  if (d1 == 1 && d2 == 1) {
    min_ans = 0;
  } else if (d1 == 2 || d2 == 2) {
    min_ans = 1;
  } else {
    min_ans = 2;
  }

  max_ans = max(d1, d2) - 1;

  cout << min_ans << "\n" << max_ans << "\n";

  return 0;
}

```