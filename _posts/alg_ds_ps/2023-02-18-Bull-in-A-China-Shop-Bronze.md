---
layout: single
title:  "[USACO] Bull in A China Shop (Bronze)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - alg_ds_ps
use_math: true
---

문제: [Bull in A China Shop (Bronze)](http://www.usaco.org/index.php?page=viewproblem2&cpid=640)

황소 피규어가 두동강났다. 다양한 피규어 조각들$$K (3 \leq K \leq 10)$$이 주어졌을때 원래 황소 피규어를 구성하고 있던 두 조각은 무엇일까?

### Approach 1
단순하게 어떤 두 개의 조각을 고르고 하나의 조각은 고정(아래 그림에서는 파란색 정사각형으로 표현 조각)시켜놓은 상태에서 다른 조각을 2차원 평면상에서 아래처럼 슬라이딩(빨간색 정사각현으로 표현한 조각)시켜가면서 이게 원본의 조각과 같은지 확인하는 작업을 해보면 된다. 

![bull in a china shop img 1](/assets/image/alg_ds_ps/bull_in_a_china_shop_bronze/bull_in_a_china_shop_bronze_img_1.png)
위에 상황에서는 아직 오른쪽으로 이동하는 것이 의미가 있기때문에 오른쪽으로 슬라이딩

![bull in a china shop img 2](/assets/image/alg_ds_ps/bull_in_a_china_shop_bronze/bull_in_a_china_shop_bronze_img_2.png)
위에 상황에서는 더이상 오른쪽으로 이동이 의미가 없기에 다음 행의 첫번째 열로 이동.

![bull in a china shop img 3](/assets/image/alg_ds_ps/bull_in_a_china_shop_bronze/bull_in_a_china_shop_bronze_img_3.png)
위에 상황에서 원본 피규어와 같은 상황에 드디어 맞닥들이게 됨.

그렇다면 어떻게 고정된 조각과 슬라이딩되어지는 조각이 원본과 같은지 판별해낼까?

![bull in a china shop img 4](/assets/image/alg_ds_ps/bull_in_a_china_shop_bronze/bull_in_a_china_shop_bronze_img_4.png)

위의 그림에서처럼 임의로 기준점을 잡는다 (우리의 경우 가장 왼쪽 위에 있는 실제 조각의 부분 #)을 기준으로 잡았다. 

이 기준 위치로부터 나머지 #들의 상대적인 위치 정보들을 구하고 이를 통해 원본과 일치하는지 효율적으로 구해낼 수 있다.

그럼 위의 아이디어가 실현가능한지 시간복잡도를 따져보자.

먼저 두개의 조각을 고르는 것은 총 $$O(K * (K - 1) / 2) \approx O(K^2)$$의 경우의 수를 고려해야한다.

어떤 두 조각들을 고른 후 슬라이딩해보는 과정은 하나의 면의 길이가 $$N (1 \leq N \leq 8)$$인 정사각형 조각들을 가능한 모든 조립의 경우의 수를 고려하기 위해서는 대략적으로 이동가능한 각각 행과 열의 범위는 $$2 * N + 1$$이다. 

즉 2차원 평면에서 행과 열에 대해 모든 슬라이딩 가능한 경우의 수를 생각하면 $$O((2 * N + 1)^2) \approx O(N^2) $$이다.

그리고 마지막으로 어떠한 방식에 의해 조립된 두 조각들이 원본과 일치하는지 확인하는 작업은 하나의 정사각형 조각의 한면의 길이가 $$N$$으로 두고 계산하면(귀찮아서 최적화 안하고 커버가능한 모든 영역을 대충구하면) $$O((3 * N)^2\ \lg\ (3 * N)^2)$$의 시간복잡도를 통해 조합된 조각들에 포함된 #들의 상대 위치들을 구하는데 든다. 

그리고 사실 조합된 조각들로부터 구한 #들의 상대위치들과 원본조각에 포함된 #들의 상대위치들을 비교하는 작업은 각 정사각형 조각이 최대 $$N^2$$개의 #을 포함가능하므로 (행 먼저 그 다음에 열을 기준으로 정렬되어있다 가정했을때)검사시간은 $$O(N^2)$$이 걸린다.

총 시간복잡도는 Constant factor를 고려해서 $$O(K*(K-1)/2 * (2 * N + 1)^{2} * (3*N)\ lg\ (3*N)) \approx O(45 * 17 ^ 2 * 24^2 \lg 24^2) \approx O(7e7)$$ 정도로 제한시간내에 풀 수 있다. 

###Implemenation
```cpp
// Bull in A China Shop
// http://www.usaco.org/index.php?page=viewproblem2&cpid=640

#include <bits/stdc++.h>

using namespace std;

char board[24][24];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N, K;
  cin >> N >> K;

  set<pair<int, int>> orig;
  int start_x = -1, start_y;
  for (int i = 0; i < N; ++i) {
    string s;
    cin >> s;
    for (int j = 0; j < N; ++j) {
      if (s[j] == '.') continue;
      if (start_x == -1) {
        start_x = i;
        start_y = j;
      }
      orig.insert({i - start_x, j - start_y});
    }
  }

  vector<vector<string>> pieces(K, vector<string>(N));
  for (int i = 0; i < K; ++i) {
    for (int j = 0; j < N; ++j) {
      cin >> pieces[i][j];
    }
  }

  int ans_x = -1, ans_y;
  for (int i = 0; i < K - 1; ++i) {
    for (int j = i + 1; j < K; ++j) {  // 45
      for (int x = 0; x <= 2 * N; ++x) {
        for (int y = 0; y <= 2 * N; ++y) {  // 289 * 45
          for (int a = 0; a < 3 * N; ++a) {
            for (int b = 0; b < 3 * N; ++b) {
              board[a][b] = '.';
            }
          }

          for (int a = 0; a < N; ++a) {
            for (int b = 0; b < N; ++b) {
              if (pieces[j][a][b] == '.') continue;
              board[N + a][N + b] = pieces[j][a][b];
            }
          }

          bool ok = true;
          for (int a = 0; a < N; ++a) {
            for (int b = 0; b < N; ++b) {
              if (pieces[i][a][b] == '.') continue;
              if (board[x + a][y + b] == '#') {  // overlap
                ok = false;
                break;
              }
              board[x + a][y + b] = pieces[i][a][b];
            }
            if (ok == false) break;
          }

          if (ok == false) continue;  // overlap, no further check is needed

          set<pair<int, int>> cand;
          int start_x = -1, start_y;
          for (int a = 0; a < 3 * N; ++a) {
            for (int b = 0; b < 3 * N; ++b) {
              if (board[a][b] == '.') continue;
              if (start_x == -1) {
                start_x = a;
                start_y = b;
              }
              cand.insert({a - start_x, b - start_y});
            }
          }

          if (orig == cand && ans_x == -1) {
            ans_x = i + 1;
            ans_y = j + 1;
          }
        }
      }
    }
  }

  cout << ans_x << " " << ans_y << "\n";

  return 0;
}
```

