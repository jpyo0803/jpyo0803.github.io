---
layout: single
title:  "[USACO] Cowntact Tracing (Bronze)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - alg_ds_ps
use_math: true
---

문제: [Cowntact Tracing (Bronze)](http://www.usaco.org/index.php?page=viewproblem2&cpid=1037)

최초에 한 마리의 소(Patient zero)가 COWVID-19 바이러스를 갖고 시작한다고 한다. 

어떤 바이러스에 걸린 소는 악수를 통해 다른 소에게 바이러스를 $K$번 전파시킬 수 있다. 

그리고 한번 바이러스에 걸린 소는 걸린 시점으로부터 $K$번 다른 소(이미 바이러스에 걸려있던 말던 상관없이, 즉 이미 바이러스가 걸린 소와 접촉해도 총 $K$번에서 1회 감소)에게 바이러스를 전파시킨다. 

그렇다면 최종적으로 바이러스에 걸린 소들의 목록과 어떤 소들이 악수를 언제 했는지에 대한 정보들($1 \leq T \leq 250$)이 주어진다면 Patient zero가 될 수 있는 후보들의 수와 최소 및 최대 $K$값들을 어떻게 알아낼 수 있을까?

### Approach 1
이 문제를 풀기 위해 가장 쉬운 방법은 몇 가지를 가정한 후에 시뮬레이션을 돌려보는 것이다.

시뮬레이션에 필요한 가정
- 각 소를 Patient zero라고 가정. 소들의 마리수는 $N (1 \leq N \leq 100)$ 으로 표현.
- 어떤 소를 Patient zero라고 가정한 후. $K$를 가정해본다. 소들이 악수를 할 수 있는 최대 횟수는 250번($1 \leq T \leq 250$)임을 고려하면 $K$값은 $[0, 250]$까지 각각 가정해보면 된다.

위의 가정을 통해 우리는 악수가 일어난 시간순으로 시뮬레이션을 해볼 수 있다. 

시뮬레이션에 앞서 어떤 소들이 언제 악수를 하였는지에 대한 정보 $(t, x, y)$가 주어지는데 시간순으로 정렬이 되어있지 않기때문에 먼저 정렬부터 해준다. 정렬을 위한 시간복잡도는 $O(T\ lg\ T)$.

시뮬레이션에서는 앞서 정렬한 악수 정보를 한번씩 시뮬레이션 해본다. 각 시뮬레이션을 위한 시간복잡도는 $O(T)$

즉, 모든 경우에 대해 시뮬레이션을 하기 위해 드는 시간복잡도는 $O(N * T^{2} + T\ lg \ T) \approx O(N * T^{2})$이다. 

각 시뮬레이션을 통해 얻어낸 결과가 최초에 입력으러 주어진 결과 정보와 일치하면 Patient zero가 될 수 있는 소의 마리수를 증가시킨다. 

마찬가지로 최소 $K$값은 각 시뮬레이션에서 나온 결과가 입력에서 주어진 결과와 일치했을때의 최소 $K$값을 취하게 한다.

반면에 최대 $K$값은 $\infty$가 될 수 있다. 그렇다면 어떤 상황에서 최대값이 무한대가 될까? 그리고 우리는 이것을 어떻게 알 수 있을까? 

생각해보면 이러한 사실을 알아내는 것이 그리 어렵지 않다는 것을 알 수 있다. 

앞서 우리는 현재 문제에서 주어지는 악수 정보는 최대 250개임을 착안해 어떤 Patient zero는 아무리 많아봤자 최대 250마리의 다른소(사실 엄밀하게는 100마리이다)에게 전파시킬 수 있음을 알 수 있다.

$K$값을 250보다 조금 더 큰 숫자 (충분히 큰 숫자)로도 시뮬레이션을 해보는 것이다. 

즉, 아무리 $K$를 늘려 시뮬레이션을 돌려도 어느 순간 결과가 변하지 않는다면 $K$값을 무한대라고 해도 괜찮다는 것이다. (살짝 Bellman-ford 최단거리 알고리즘에서 음수 사이클 여부를 판별하는 것이랑 비슷한 것 같다.)

### Implementation
```cpp
// Cowntact Tracing
// http://www.usaco.org/index.php?page=viewproblem2&cpid=1037

#include <bits/stdc++.h>

using namespace std;

struct Record {
  int t, x, y;
};

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N, T;
  cin >> N >> T;

  string s;
  cin >> s;

  vector<bool> result(N);
  for (int i = 0; i < s.length(); ++i) {
    result[i] = (s[i] == '1');
  }

  vector<Record> r(T);
  for (int i = 0; i < T; ++i) {
    cin >> r[i].t >> r[i].x >> r[i].y;
    r[i].x--;
    r[i].y--;
  }

  // sort chronically
  sort(r.begin(), r.end(),
       [](const Record& a, const Record& b) { return a.t < b.t; });

  int num_cand = 0, min_k = 1e9, max_k = 0;
  for (int i = 0; i < N; ++i) {  // assume i-th cow is patient 0

    bool ok = false;
    for (int j = 0; j <= 260;
         ++j) {  // assume K, use value larger than 250, to see its infiniteness
      vector<bool> simul(N, false);
      simul[i] = true;

      vector<int> k_try(N, j);

      for (int k = 0; k < T; ++k) {  // iterate over individual records
        bool after_x = false, after_y = false;
        if (simul[r[k].x] && k_try[r[k].x] > 0) {
          after_y = true;
          k_try[r[k].x]--;
        }
        if (simul[r[k].y] && k_try[r[k].y] > 0) {
          after_x = true;
          k_try[r[k].y]--;
        }
        simul[r[k].x] = simul[r[k].x] | after_x;
        simul[r[k].y] = simul[r[k].y] | after_y;
      }

      if (result == simul) {
        ok = true;
        min_k = min(min_k, j);
        max_k = max(max_k, j);
      }
    }
    if (ok) num_cand++;
  }

  cout << num_cand << " " << min_k << " ";
  if (max_k == 260) {
    cout << "Infinity\n";
  } else {
    cout << max_k << "\n";
  }

  return 0;
}
```