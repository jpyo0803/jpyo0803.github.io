---
layout: single
title:  "[USACO] Stuck in A Rut (Bronze)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - algorithm_ps
use_math: true
---

문제: [Stuck in A Rut (Bronze)](http://www.usaco.org/index.php?page=viewproblem2&cpid=1061)

![stuck in a rut img 1](/assets/image/algorithm_ps/stuck_in_a_rut_bronze/stuck_in_a_rut_bronze_img_1.png)
$nc = cow\ moving\ north\ (+y)$\
$ec = cow\ moving\ east\ (+x)$

### Approach 1 (Simple and intuitive)

이 문제를 푸는 가장 간단한 방법은 직접 소들을 정해진 방향대로 1칸씩 전진시키는 시뮬레이션을 해보는 것이다. 

어떤 소를 전진 시키고자할때 진행하는 방향의 바로 앞 칸의 풀을 이미 다른 소가 과거에 먹었다면 멈추면 된다.

만약 북쪽과 동쪽으로 진행하는 소들이 동시에 어떤 칸으로 전진하게되는 상황에서는 그냥 교차하여 멈추지 않고 지나치면 된다.

$N= the\ number\ of\ cows$


위 알고리즘의 시간복잡도는 대략적으로 $O(max(x, y) * N)$ 정도이다. 왜냐하면 총 $N$마리의 소들을 최대 $max(x,y)$만큼 전진시켜야 하기 때문이다.  

이 방식은 주어진 조건인 들판의 크기 $x, y$의 값들이 충분히 작아야 시간내에 문제를 풀 수 있다. 

하지만 주어진 $x$, $y$, $N$의 값은 $0 \leq x, y \leq 1e9$ 와 $1 \leq N \leq 50 $이다. 

즉, 최악의 경우 $O(1e9 * 50)$ 정도의 시간복잡도이며 이는 주어진 1~2초내에 문제해결이 불가능함을 시사한다. 

그뿐 아니라 위의 아이디어를 구현하려면 $1e9 * 1e9$ 크기의 배열이 필요하고 메모리적인 측면에서도 위의 아이디어 구현을 불가능하게 한다.

### Approach 2 (Viable but complex)

그렇다면 매 시뮬레이션마다 한칸씩 전진시키기 보다 충분히 많이 전진시키는 방식을 떠올릴 수 있겠다.

그렇다면 한번에 얼마나 많이 전진 시키는 것이 문제를 현실적인 시간내에 풀 수 있을까? 만약 최소 한마리의 소가 또 다른 소에 의해 정지가 가능한 상황이라면 현재 상황에서 정지하기까지 최소의 거리를 필요하는 소를 찾고 그 최소 거리를 이번 시뮬레이션에서 어떤 소가 전진해야하는 거리로 사용하면 된다.

![stuck in a rut img 2](/assets/image/algorithm_ps/stuck_in_a_rut_bronze/stuck_in_a_rut_bronze_img_2.png)

위의 다이어그램에서 선택된 (빨간 타원) 경우를 보면 동쪽으로 전진하는 소가 북쪽으로 전진하는 소에 의해 정지 당하기까지 거리 2만큼이 필요한 경우이다. (최소 거리)

즉, 최소 한마리의 소가 정지당하기까지 필요한 최소 거리인셈이다. 

여기서 확인가능한 invariant는 매 시뮬레이션마다 최소 한마리의 소가 정지하기때문에 최악의 경우 $N$번만큼만 시뮬레이션을 해보면 된다는 것이다. 

Approach 1에서는 최악의 경우 $max(x,y)$ 번의 시뮬레이션이 필요했던 것과 상반된다.

또한 배열에서 소들의 위치를 표현할 필요없이 각각의 소의 위치를 표현하는 $x$와 $y$의 값만 업데이트해주면 되기때문에 공간적인 측면에서도 현실적이게 된다.

그렇다면 어떤 소가 또 다른 소에 의해 정지당하는지 또 만약 정지당한다면 얼마만큼의 거리가 필요한지 어떻게 알아낼 수 있을까?

#### 어떤 소 A가 또 다른 소 B에 의해 정지 당하는 경우와 당하지 않는 경우

여기서 편의상 소 A는 북쪽으로 **아직 진행 중**인 소라고 하고 소 B는 동쪽으로 진행 또는 이미 정지한 소라고 하자.

#### Case 1: $B.y \leq A.y$ (A는 B에 의해 정지하지 못함)

![stuck in a rut img 3](/assets/image/algorithm_ps/stuck_in_a_rut_bronze/stuck_in_a_rut_bronze_img_3.png)
만약 $B.y < A.y$ 라면 소 B는 소 A를 어떠한 경우에도 정지 시키지 못한다.

위 상황은 $B.y$ (소 B의 $y$ 위치)가 $A.y$ (소 A의 $y$ 위치)보다 같거나 아래에 위치할때이다. 

![stuck in a rut img 4](/assets/image/algorithm_ps/stuck_in_a_rut_bronze/stuck_in_a_rut_bronze_img_4.png)

위 상황은 $B.y$의 위치가 $A.y$의 위치가 같은 경우이다. 

이는 지금 설명하는 알고리즘에서 일어날수 없는 경우이다. 좀 더 설명을 하자면 우리의 관심사는 **현재 전진**하는 소 A가 **현재 전진할수도 아니면 이미 정지해버린** 소 B에 의해 정지 당하는 것이 가능한지 또 가능하다면 거리는 얼마나 필요한지이다. 

만약 $B.y = A.y$ 인 상황은 총 세가지 경우가 있다. 
  1. 맨 처음부터 $A.x < B.x$인 경우인데, 이 경우는 애초에 A와 B는 교차할 수 없다.
  2. 맨 처음에는 $B.x \leq A.x$ 였지만 현재는 $A.x < B.x$인 경우인데, 이는 위의 알고리즘을 따른다면 실제 일어날 수 없는 경우이다. (이미 이전에 B에 의해 A는 정지하게 되고 A를 기준으로 알고리즘을 다시 시행하지 않을 것 이기 때문이다.)
  3. 현재 $B.x < A.x$ 인 경우는 A와 B는 교차할 수 없다. ($B.x$가 $A.x$에 도달했을때 $B.y < A.y$ 일 것이기 때문에) 

#### Case 2 (B가 아직 이동중이라면 A를 정지할 수 있음)

![stuck in a rut img 5](/assets/image/algorithm_ps/stuck_in_a_rut_bronze/stuck_in_a_rut_bronze_img_5.png)

아직 소 B가 소 A위를 지나지 않았고 아직 움직이는 상황이라면 소 B가 노란색 삼각형으로 표현된 영역안에 있어야 추후에 소 A를 정지시킬 수 있다.

이를 수식으로 표현하자면 아래의 조건을 만족해야한다.
- condition 1: $A.x + A.y < B.x + B.y$
- condition 2: $B.x \leq A.x$

#### Case 3 (현재 $B.y < A.y$)
이 경우는 B가 A를 정지시킬 가능성은 존재하지 않는다.

만약 현재 $A.x < B.x$ 인 경우에도 처음부터 $A.x < B.x$ 인 경우에는 애초에 교차할 수 없었고 최초에 $B.x <= A.x$인 경우였다가 $A.x < B.x$인 경우는 실제로 일어나지 않는다. (만약 A가 B에 의해 이전에 막혔다면 A가 다시 선택되지 않았을 것이다.)

#### 매 시뮬레이션마다 멈추어야 하는 소
위의 세가지 Case를 토대로 아직 **전진 중인** 각 소 A가 다른 어떤 소 B에의해 정지당하는 최소거리를 구한다. 각 소에 대해 정지를 위한 최소 거리들을 구하고 그 최소 거리들 중 최소 거리를 이번 시뮬레이션에서 아직 **전진 중인** 소가 얼마나 이동할지 결정하는데 사용한다.

위 과정을 더 이상 정지하는 소가 존재하지 않을때까지 반복한다.

#### 시간복잡도 
매 시뮬레이션마다 최소 1마리의 소가 정지하는 사실을 기반으로 우리는 최대 N번의 시뮬레이션이 필요한 것을 알 수 있다.

매 시뮬레이션마다 어떤 소 A가 다른 소 B에 의해 정지하는지 또 정지한다면 얼마의 거리를 필요로하는지 구하는데 $O(N^2)$가 걸린다.

종합해보자면 $O(N^2)$의 시간복잡도를 갖는 시뮬레이션이 최대 N번 시행되어야하므로 총 시간복잡도는 $O(N^3)$이다. 

#### 구현
```cpp
// Stuck in A Rut
// http://www.usaco.org/index.php?page=viewproblem2&cpid=1061

#include <bits/stdc++.h>

using namespace std;

struct Cow {
  char dir;
  int x, y;
  int moves;

  Cow(char _dir, int _x, int _y) : dir(_dir), x(_x), y(_y), moves(-1) {}
};

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N;
  cin >> N;

  vector<Cow> cows;
  for (int i = 0; i < N; ++i) {
    char dir;
    int x, y;
    cin >> dir >> x >> y;
    cows.emplace_back(dir, x, y);
  }

  int t = 0;  // current time
  while (true) {
    int min_bt = -1;
    vector<int> ebt(N, -1);

    for (int i = 0; i < N; ++i) {
      if (cows[i].moves != -1) continue;  // already blocked

      for (int j = 0; j < N; ++j) {
        if (cows[i].dir == cows[j].dir)
          continue;  // same dir cannot block each other

        Cow nc = cows[i];
        Cow ec = cows[j];
        if (nc.dir == 'E') {
          swap(nc.x, nc.y);
          swap(ec.x, ec.y);
        }

        if (ec.y <= nc.y) continue;

        if (ec.moves == -1) {  // still moving
          if (nc.x < ec.x - t)
            continue;  // from the beginning, it already passed nc.x
          if (ec.x + ec.y <= nc.x + nc.y) continue;
        } else {                      // already stopped
          if (ec.x < nc.x) continue;  // stopped even before blocking nc
          if (nc.x < ec.x - ec.moves) continue;
        }
        int bt = t + ec.y - nc.y;
        if (ebt[i] == -1 || bt < ebt[i]) {
          ebt[i] = bt;
        }
      }

      if (ebt[i] != -1) {
        if (min_bt == -1 || ebt[i] < min_bt) {
          min_bt = ebt[i];
        }
      }
    }

    if (min_bt == -1) break;  // no more blocking

    for (int i = 0; i < N; ++i) {
      if (cows[i].moves != -1) continue;

      if (cows[i].dir == 'N') {
        cows[i].y += min_bt - t;
      } else {
        cows[i].x += min_bt - t;
      }

      if (ebt[i] == min_bt) {
        cows[i].moves = min_bt;
      }
    }

    t = min_bt;
  }

  for (int i = 0; i < N; ++i) {
    if (cows[i].moves == -1) {
      cout << "Infinity\n";
    } else {
      cout << cows[i].moves << "\n";
    }
  }

  return 0;
}
```

### Comment
위에 기술한 알고리즘보다 훨씬 효율적인 알고리즘도 존재하는 것으로 알고 있다. 이는 더 높은 단계 (silver나 gold)에서 다루면 좋을 것 같다.