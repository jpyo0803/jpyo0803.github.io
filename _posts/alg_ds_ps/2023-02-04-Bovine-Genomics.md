---
layout: single
title:  "[USACO] Bovine Genomics (Bronze)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - alg_ds_ps
use_math: true
---

문제: [Bovine Genomics (Bronze)](http://www.usaco.org/index.php?page=viewproblem2&cpid=736)

$N$마리의 spotty 소들과 또 다른 $N$마리의 plain 소들이 존재한다. 각 소들은 길이 $M$의 DNA Sequence 정보를 갖고 있다. 좀 더 자세한 구성은 아래를 보자.

```txt
Positions:    1 2 3 4 5 6 7 ... M

Spotty Cow 1: A A T C C C A ... T
Spotty Cow 2: G A T T G C A ... A
Spotty Cow 3: G G T C G C A ... A

Plain Cow 1:  A C T C C C A ... G
Plain Cow 2:  A C T C G C A ... T
Plain Cow 3:  A C T T C C A ... T
```

이 문제에서 우리의 관심사는 어떤 position에서 어떤 DNA Sequence를 구성하는 A, C, G, T 문자들 중 하나의 문자가 주어지면 이 문자를 포함하는 소는 Spotty인지 Plain인지 **항상** 구분 가능한 position의 개수가 몇 개인지 구하는 것이다.

### Approach 1
이 문제를 풀기 위한 아이디어는 간단하다. 어떤 position에서 Spotty 소들과 Plain 소들이 각각 포함하고 있는 문자들이 겹치지 않는다면 해당 position은 어떠한 문자가 주어져도 이 문자를 포함하는 소가 Spotty인지 Plain인지 구분가능하다.

#### 구분가능한 Case (Spotty/Plain 그룹간 겹치는 문자가 없음)
![bovine genomics img 1](/assets/image/alg_ds_ps/bovine_genomics/bovine_genomics_img_1.png)
이 케이스에서는 문자 C가 주어지면 우리는 해당 문자를 DNA Sequence에 포함한 소가 Spotty함을 알 수 있다. 반면에 주어진 문자가 G라면 이는 Plain한 소임을 의미한다. 
만약 양쪽에 모두 포함되지 않은 문자 T가 주어지면 문제 설명에서 처럼 무시해도 된다.

#### 구분 불가능한 Case (Spotty/Plain 그룹간 겹치는 문자가 존재)
![bovine genomics img 1](/assets/image/alg_ds_ps/bovine_genomics/bovine_genomics_img_2.png)

이번에는 문자 C가 양쪽 그룹에 모두 포함되어 있다. 만약 문자 C가 주어진다면 해당 문자를 포함하는 소가 Spotty인지 Plain인지 분류가 불가능한 상황이 생긴다. 즉, 항상 구분할수 없다.

즉, Spotty와 Plain 그룹들간에 전혀 DNA 문자(A, C, G, T)를 공유하고 있지않은 position의 개수를 세면 된다. 시간복잡도는 $O(N * M)$.

#### Implementation
위의 아이디어를 구현하면 아래와 같다.
```cpp
// Bovine Genomics (Bronze)
// http://www.usaco.org/index.php?page=viewproblem2&cpid=736

#include <bits/stdc++.h>

using namespace std;

int Seq2Idx(char s) {
  if (s == 'A')
    return 0;
  else if (s == 'C')
    return 1;
  else if (s == 'G')
    return 2;
  return 3;
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N, M;
  cin >> N >> M;

  vector<string> s(N), p(N);
  for (int i = 0; i < N; ++i) {
    cin >> s[i];
  }
  for (int i = 0; i < N; ++i) {
    cin >> p[i];
  }

  int ans = 0;
  for (int i = 0; i < M; ++i) {
    int s_cnt[4]{};
    int p_cnt[4]{};

    for (int j = 0; j < N; ++j) {
      ++s_cnt[Seq2Idx(s[j][i])];
      ++p_cnt[Seq2Idx(p[j][i])];
    }

    // Check if overlapping gene type exists.
    // If exist, given the overlapping, you cannot tell
    bool ok = true;
    for (int j = 0; j < 4; ++j) {
      if (s_cnt[j] > 0 && p_cnt[j] > 0) {
        ok = false;
        break;
      }
    }
    if (ok) {
      ++ans;
    }
  }

  cout << ans << "\n";

  return 0;
}
``` 