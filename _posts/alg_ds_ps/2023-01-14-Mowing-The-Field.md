---
layout: single
title:  "[USACO] Mowing the Field"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - alg_ds_ps
use_math: true
---

문제: [Mowing the Field](http://www.usaco.org/index.php?page=viewproblem2&cpid=593)

### 재방문까지 걸린 시간 구하기
농부 존은 입력에서 주어진 경로를 따라서 들판(field?)의 잔디를 깎는다. 
들판의 잔디가 자라는 속도는 균일하다. 
그리고 경로를 세심하게 계획하지 않았기 때문에 과거에 이미 깎았던 장소로 돌아올 수 있다. 

문제를 잘 읽어보면 중요한 사실을 하나 알 수 있다. 농부 존이 어떤 경로를 따라가며 어떤 장소에 다시 방문했을때 잔디는 항상 다시 자라있었다는 것이다.

우리가 이 문제에서 해결하고자 하는 것은 최소의 잔디 성장 속도를 찾는 것이다. 
이 문제를 풀기 위해 우리는 어떤 장소에 재방문하였을때 저번에 방문하였을때의 시간을 알수있어야 한다. 
 
![mowing_the_field_img_1](/assets/image/alg_ds_ps/mowing_the_field_img_1.png)
위 예지를 통해 만약 어떤 cell에 대해 지난번 방문한 시간 $x$를 알고 있다면 현재시간 $y$에 $x$를 빼는 방식으로 재방문까지 걸린 시간을 계산할 수 있다.

이는 들판의 각 단위 cell에 타임스탬프를 기록해둘 수 있는 저장공간을 두어 어떤 cell에 재방문시 재방문까지 걸린 시간을 계산해낼 수 있다. 

그렇다면 타임 스탬프를 기록할 수 있는 들판을 표현하기 위한 2차원 배열의 크기는 얼마나 되어야할까? 

문제에서 주어진 조건에서는 최대 $N=100$ 방향의 sequence가 가능하고 각 sequence는 최대 10번 해당 방향으로 전진할 수 있음을 알 수 있다. 

즉, 최악의 경우 한쪽방향으로 최대 1000칸 이동하는 경우가 존재한다.

이동중 2차원 배열의 범위를 넘지 않도록 하기 위해 (농부 존의 시작지점이 $x=1050, y=1050$ 라면) 
배열의 크기는 대략적으로 **field[2100][2100]** 정도면 충분 할 것이다.

### 잔디 다시 자라기까지 걸리는 최대 시간 구하기
농부 존은 입력으로 주어진 경로를 따라 잔디를 깎아가며 잔디가 다시 원래대로 자라기까지 적어도 걸리는 시간을 구해야한다. 

이는 꽤 간단하게 구할 수 있다. 재방문까지 걸리는 시간들 중 가장 긴 시간을 구하면 된다.

![mowing_the_field_img_2](/assets/image/alg_ds_ps/mowing_the_field_img_2.png)

위의 논리를 정당화해보자. 

잔디가 자라기까지 걸리는 최위 예제에서 3번 경로는 재방문까지 35단위시간이 걸린다. 

만약 35단위시간이 잔디가 다시 원상복구되기에 적어도 걸리는 시간이라고하면 길이가 10인 1번 경로와 모순된다. 
(농부 존은 어떠한 cell에 재방문했을때 항상 해당 cell의 잔디는 다시 자라있었다고 하였다.)


### Implementation
```cpp
// Mowing the Field
// http://www.usaco.org/index.php?page=viewproblem2&cpid=593

#include <bits/stdc++.h>

using namespace std;

int dx[4] = {0, 0, 1, -1};
int dy[4] = {1, -1, 0, 0};

// FJ's starting point will be 1050, 1050
// 2100 by 2100 is big enough for any direction
// since N = 100, S = 10
int field[2100][2100];  // contains last access time

int N;

int GetDirIdx(char dir) {
  if (dir == 'E')
    return 0;
  else if (dir == 'W')
    return 1;
  else if (dir == 'S')
    return 2;
  return 3;
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  cin >> N;

  int ans = -1;

  int curr_x = 1050, curr_y = 1050;
  int t = 1;
  field[curr_x][curr_y] = t;

  for (int i = 0; i < N; ++i) {
    char D;
    int S;
    cin >> D >> S;

    int dir = GetDirIdx(D);
    while (S--) {
      ++t;
      curr_x += dx[dir];
      curr_y += dy[dir];

      // If the next cell had been visited before
      if (field[curr_x][curr_y] != 0) {
        int dt = t - field[curr_x][curr_y];
        if (ans == -1 || dt < ans) {
          ans = dt;
        }
      }
      field[curr_x][curr_y] = t;
    }
  }
  cout << ans << "\n";

  return 0;
}
```