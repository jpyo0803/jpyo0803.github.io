---
layout: single
title:  "[USACO] Measuring Traffic"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - algorithm_ps
use_math: true
---

문제: [Measuring Traffic](http://www.usaco.org/index.php?page=viewproblem2&cpid=917)

이 문제는 총 N-mile 길이의 도로에서 각 1-mile segment마다 세가지 다른 종류의 도로가 존재한다.

도로가 가로 내어져있고 있고 차량은 오른쪽(or 동쪽) 방향으로 이동하는 것이 정방향이라고 가정했을때 아래와 같은 3개의 다른 종류의 mile segment가 존재한다.
- on-ramp: 차량이 도로로 진입 통행량. 
- off-ramp: 차량이 도로에서 진출 통행량.
- none: 진입도 진출도 아님. 현재 도로의 통행량 

최종적으로 우리가 얻고자하는 것은 주어진 입력조건을 위배하지 않으며 도로의 시작지점 직전과 끝지점 직후에서 가능한 통행량의 범위이다. 

이해를 돕기 위해 간단한 예를 들어보자,

!["measuring_traffic_img_1"](/assets/image/algorithm_ps/measuring_traffic_img_1.png)

위 그림에서 검은 선으로 표시된 도로는 주도로(main road)이다. 빨간색으로 표시된 도로는 "on-ramp" 도로이다. 파란색으로 표시된 도로는 "off-ramp"이다. 앞서에서의 가정처럼 차량의 흐름이 가로로된 도로의 오른쪽 방향이라고 하자. 

- 편의상 t1 시점 이전의 통행량은 차량 100대가 최초에 있다고 하자. 
- 그런 후 on-ramp에서 10~50대의 차량이 주도로로 진입하였으므로 t2 시점 직후에 가능한 차량 통행량의 범위는 110~150대이다. 
- 마지막으로 off-ramp에서 10~15대의 차량이 주도로에서 진출한다. t3 시점 직후에 가능한 차량 통행량의 범위는 95~140대이다. 


### on-ramp와 off-ramp의 통행량 정보 활용
우리는 위에서의 예제를 통해 on-ramp나 off-ramp 직후 차량의 통행량을 구하는데 유용한 몇가지 사실들을 알아낼 수 있다.
- 사실1: on-ramp에서 주어진 진입 통행량의 **최소 / 최대**값은 현재까지 주도로에서의 통행량의 **최소 / 최대**값에 각각 더해진다. 
  - on-ramp 직후의 **최소** 통행량 = on-ramp 직전의 주도로에서의 **최소** 통행량 + on-ramp에서의 **최소** 통행량 
  - on-ramp 직후의 **최대** 통행량 = on-ramp 직전의 주도로에서의 **최대** 통행량 + on-ramp에서의 **최대** 통행량
- 사실2: off-ramp에서 주어진 진입 통행량의 **최소 / 최대**값은 현재까지 주도로에서의 통행량의 **최대 / 최소**값에 각각 더해진다.
  - off-ramp 직후의 **최소** 통행량 = off-ramp 직전의 주도로에서의 **최소** 통행량 + off-ramp에서의 **최대** 통행량
  - off-ramp 직후의 **최대** 통행량 = off-ramp 직전의 주도로에서의 **최대** 통행량 + off-ramp에서의 **최소** 통행량

### 주도로의 통행량 정보 활용
현재 직전까지의 **최소 / 최대** 통행량과 현재 주어진 **최소 / 최대** 통행량의 정보를 종합해 주도로의 통행량 범위를 최소화한다. 
- 사실3: 새롭게 주어진 주도로의 **최소 / 최대** 통행량 정보를 지금까지의 **최소 / 최대** 통행량 정보와 종합해 주도로의 **최소 / 최대** 통행량 범위를 최소화한다.

### 주도로의 통행량이 알려지기전에 on-ramp나 off-ramp가 존재한다면?
!["measuring_traffic_img_2"](/assets/image/algorithm_ps/measuring_traffic_img_2.png)
위 예제에서 t1 시점 이전의 통행량이 알려져있지 않은 상태에서 10~50대의 차량이 on-ramp에서 진입을 한다고해도 t2 직후의 통행량을 알아낼 수 없을 것이다. 즉, t3에 도달해야만 주도로의 통행량을 알 수 있다. 다시말해 주도로의 통행량이 알려지지 않은 상태에서 on-ramp에서의 진입 통행량 정보는 사실상 사용되지 않는다. 이러한 사실은 off-ramp의 경우에도 똑같이 적용된다.

- 사실4: 주도로의 통행량 범위가 주어지지 않은 상태에서 on-ramp와 off-ramp의 통행량은 결과값에 영향을 주지 못한다. 
  - Solution: 구현상에서는 통행량의 **최소 / 최대**값을 각각 0과 1e9(매우 큰값)으로 두면 자연스럽게 문제가 해결된다.

### Implementation
NOTE:구현시 입력에 따라 최소 통행량이 음수가 되는 경우가 생기는데 이를 방지하기 위해 최소 통행량은 0이상을 유지하도록 한다.

```cpp
// Measuring Traffic
// http://www.usaco.org/index.php?page=viewproblem2&cpid=917

#include <bits/stdc++.h>

using namespace std;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N;
  cin >> N;

  vector<tuple<string, int, int>> info(N);
  for (int i = 0; i < N; ++i) {
    string a;
    int b, c;
    cin >> a >> b >> c;
    info[i] = tie(a, b, c);
  }

  // extreme range is approximately between 0 and 1e5
  // so initializing upper bound to 1e9 is fine
  int low = 0, high = 1e9;
  for (int i = N - 1; i >= 0; --i) {
    string a;
    int b, c;
    tie(a, b, c) = info[i];
    if (a[1] == 'f') {  // off
      low += b;
      high += c;
    } else if (a[1] == 'n') {  // on
      low -= c;
      high -= b;
    } else {  // none
      low = max(low, b);
      high = min(high, c);
    }
    // prevent either bound getting smaller than 0
    low = max(low, 0);
    high = max(high, 0);
  }

  cout << low << " " << high << "\n";

  low = 0, high = 1e9;
  for (int i = 0; i < N; ++i) {
    string a;
    int b, c;
    tie(a, b, c) = info[i];
    if (a[1] == 'n') {
      low += b;
      high += c;
    } else if (a[1] == 'f') {
      low -= c;
      high -= b;
    } else {
      low = max(low, b);
      high = min(high, c), 0;
    }
    low = max(low, 0);
    high = max(high, 0);
  }

  cout << low << " " << high << "\n";

  return 0;
}
```