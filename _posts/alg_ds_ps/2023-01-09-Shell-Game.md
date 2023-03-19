---
layout: single
title:  "[USACO] Shell Game"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - alg_ds_ps
---

문제: [Shell Game](http://www.usaco.org/index.php?page=viewproblem2&cpid=891)

(내가 아는 선에서는) 이 문제를 풀기 위한 방법은 크게 두가지가 존재한다.

첫번째 방식은 쉽고 직관적이나 시뮬레이션을 여러번 돌려야하기에 다소 
비효율적이라고 할 수 있다. 

두번째 방법은 시뮬레이션을 한번만 돌려도 되기에 효율적이지만 처음엔 이해하기가 쉽지 않을 수 있다.

### 첫번째 방법 (Easy)

문제를 풀기 앞서 원문에서 주어진 "a pebble"을 편의상 한글로는 "조약돌"이라고 지칭하겠다. 

문제를 푸는 쉽고 직관적인 첫번째 방법은 조약돌의 초기위치를 정하고 각각의 상황에 대해 시뮬레이션을 해보고 이를 통해 얻은 결과들 중 최대값을 취하면 된다. 

문제에서 주어진 예제를 통해 첫번째 방식을 이해해보자.

#### Sample Input
```
3
1 2 1
3 2 1
1 3 1
```

#### 가정 1: 조약돌의 초기위치가 첫번째 조개껍질 아래에서 시작

1. 1번과 2번 조개껍질을 교환후 1번 조개껍질 아래 있을 것이라 추측 -> 실패 (실제로는 2번 조개껍질 아래에 위치)
2. 3번과 2번 조개껍질을 교환후 1번 조개껍질 아래 있을 것이라 추측 -> 실패 (실제로는 3번 조개껍질 아래에 위치)
3. 1번과 3번 조개껍질을 교환후 1번 조개껍질 아래 있을 것이라 추측 -> 성공 

가정 1을 통해 얻을 수 있는 점수는 1점이다.

#### 가정 2: 조약돌의 초기위치가 두번째 조개껍질 아래에서 시작

1. 1번과 2번 조개껍질을 교환후 1번 조개껍질 아래 있을 것이라 추측 -> 성공
2. 3번과 2번 조개껍질을 교환후 1번 조개껍질 아래 있을 것이라 추측 -> 성공
3. 1번과 3번 조개껍질을 교환후 1번 조개껍질 아래 있을 것이라 추측 -> 실패 (실제로는 3번 조개껍질 아래에 위치)

가정 2을 통해 얻을 수 있는 점수는 2점이다.

#### 가정 3: 조약돌의 초기위치가 세번째 조개껍질 아래에서 시작

1. 1번과 2번 조개껍질을 교환후 1번 조개껍질 아래 있을 것이라 추측 -> 실패 (실제로는 3번 조개껍질 아래에 위치)
2. 3번과 2번 조개껍질을 교환후 1번 조개껍질 아래 있을 것이라 추측 -> 실패 (실제로는 2번 조개껍질 아래에 위치)
3. 1번과 3번 조개껍질을 교환후 1치 조개껍질 아래 있을 것이라 추측 -> 실패 (실제로는 2번 조개껍질 아래에 위치)

가정 3을 통해 얻을 수 있는 점수는 1점이다.

가정 1, 2, 3의 결과를 종합해 얻을 수 있는 최대 점수는 2점임을 알 수 있다.

아래는 위의 알고리즘을 구현한 소스코드이다.
```cpp
# Shell Game Method 1 (Easy)

#include <bits/stdc++.h>

using namespace std;

int N, a, b, g;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  vector<tuple<int, int, int>> cmds;
  cin >> N;

  for (int i = 0; i < N; ++i) {
    cin >> a >> b >> g;
    cmds.emplace_back(a, b, g);
  }

  int ans = 0;

  for (int i = 1; i <= 3; ++i) { // 각 가정에 대해 시뮬레이션 해본다.
    int loc = i;

    int cnt = 0;
    for (auto cmd : cmds) {
      int a, b, g;
      tie(a, b, g) = cmd;

      if (loc == a) loc = b;
      else if (loc == b) loc = a;

      if (loc == g) ++cnt;  
    }
    ans = max(ans, cnt);
  }

  cout << ans << "\n";

  return 0;
}

```

### 두번째 방법 (Efficient)

두번째 방법은 시뮬레이션을 한번만 돌려도 되기때문에 효율적이나 처음에는 직관적이지 않다. 

이 방식을 쉽게 이해하기 위해 시뮬레이션을 시작하기전 각 조개껍질에 색(빨간색, 초록색, 파란색)을 부여하도록 하겠다. 그리고 시뮬레이션을 돌리며 매번 추측을 통해 관측된 색상의 횟수를 기록한다. 

그리고 첫번째 방법을 설명하기 위해 사용했던 문제에서 주어진 예시 입력을 여기서도 동일하게 사용하겠다.

![shell_game_img_1](/assets/image/alg_ds_ps/shell_game_img_1.png)

초기에 첫번째 조개껍질에는 빨간색, 두번째 조개껍질에는 초록색, 세번째 조개껍질에는 파란색을 각각 지정하고 시뮬레이션을 돌린 결과 

초록색 조개껍질은 2번 관찰되었고 
빨간색 조개껍질은 1번 
파란색 조개껍질은 0번 관촬되었음을 확인 할 수 있다.

그렇다면 결과적으로 조약돌을 초록색 조개껍질(두번째 조개껍질) 아래 두고 시작한다면 우리는 최대 2점을 얻을 수 있음을 한번의 시뮬레이션을 통해 구해낼 수 있다. 

아래는 위의 알고리즘을 구현한 소스코드이다.

```cpp
# Shell Game Method 2 (Efficient)
#include <bits/stdc++.h>

using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    int n;
    cin >> n;
    
    int track[3] = {0, 1, 2}; // 0 = R, 1 = G, 2 = B
    int cnt[3] = {0, 0, 0};

    for (int i = 0; i < n; ++i) {
        int a, b, g;
        cin >> a >> b >> g;

        --a, --b, --g;
        swap(track[a], track[b]);
        ++cnt[track[g]];
    }

    cout << max({cnt[0], cnt[1], cnt[2]}) << "\n";

    return 0;
}
```