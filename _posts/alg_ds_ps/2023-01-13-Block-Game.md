---
layout: single
title:  "[USACO] Block Game"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - alg_ds_ps
use_math: true
---

문제: [Block Game](http://www.usaco.org/index.php?page=viewproblem2&cpid=664)

하나의 단어 보드는 각면에 단어를 표시한다. 
예를 들어 한쪽면에 dog가 표시되어있고 반대쪽면에 cat이 표시되어잇는 단어 보드는 한쪽에서보면 dog만 보일 것이고 반대쪽에서보면 cat만 보일 것이다. 
즉, 하나의 단어 보드에 존재하는 두개의 단어들을 동시에 볼수는 없다. 이러한 단어 보드들이 총 N개가 있다.

여기서 우리가 풀고자하는 것은 N개의 단어 보드들에 대해 각각 임의로 하나의 단어만 보일때 보이는 모든 단어들을 항상 표현 가능하기 위해 필요한 minimum alphabet set이 무엇이냐는 것이다.

예를들어 car와 cat을 표현하기 위해서 필요한 minimum alphabet set은 'a' 2개, 'c' 2개, 'r' 1개, 't' 1개이다.

### Method 1 (Brute-force, 작은 입력에 대해서만 사용 가능)
이 문제를 보자마자 떠오르는 해결방법은 모든 조합을 구해보는 것이다. 모든 조합은 $$O(2^N)$$으로 구할 수 있다. 
또한 각 조합에서 최대 10개의 알파벳을 갖는 단어가 최대 N개 존재 할 수 있으니 문제를 풀기 위한 시간복잡도는 $$O(2^N * 2 * 10 * N)\approx O(N*2^N)$$이다. 

예)
```txt
3
fox box
dog cat
car bus
```

- Total 8 Possible combinations
    1. (fox, dog, car)
    2. (fox, dog, bus)
    3. (fox, cat, car)
    4. (fox, cat, bus)
    5. (box, dog, car)
    6. (box, dog, bus)
    7. (box, cat, car)
    8. (box, cat, bus)

어떠한 상황(조합)에서도 충분한 minimum alphabet set을 구한다.

위 예제에서 필요한 각 알파벳 개수는 (필요하지 않은 알파벳은 제외)
- 'a' 2개 (3, 7번조합)
- 'b' 2개 (6번 조합)
- 'c' 2개 (3, 7번 조합)
- 'd' 1개 (1, 2, 5, 6번 조합)
- ... 
- 'x' 1개 (1, 2, 3, 4, 5, 6, 7, 8번 조합)

하지만 최대 가능한 단어 보드의 개수는 N=100이다. 이는 시간복잡도 $$O(2^{100} * 2 * 10 * 100)$$ 을 시사한다. 즉 N이 조금만 커져도 사용할 수 없는 알고리즘이다.

**NOTE**: 실제 주어진 문제를 풀수없기에 구현하지 않았다.

### Method 2 (Efficient)
바로 앞서 다뤘던 방식은 큰 입력에 대해 매우 비효율적이다. 그렇다면 매우 큰 N에 대해서도 문제를 풀 수 있는 방법은 어떤 것일까?

하나의 단어보드를 기준으로 놓고 보았을때 항상 **최악의 경우**에 필요한 minimum alphabet set(하나의 단어보드만 고려했을때)을 구해보자. 

예를 들어 bbc와 bcc가 하나의 단어보드에 존재한다고 해보자. bbc와 bcc는 동시에 볼수 없다. 
만약 bbc가 보일때 필요한 알파벳 세트는 'b' 2개, 'c' 1개이다.
비슷하게 bcc가 보일때 필요한 알파벳 세트는 'b' 1개, 'c' 2개이다. 
bbc와 bcc로 구성된 단어보드는 'b'의 경우 최대 2개, 'c'의 경우 최대 2개가 필요하다. 

이를 확장시켜 만약 bbc와 bcc로 구성된 똑같은 단어보드가 3개 존재한다고 해보자.

예)
```txt
3
bbc bcc
bbc bcc
bbc bcc
```

만약 조합이 (bbc, bbc, bbc)라면 포함된 단어들을 모두 표현하기 위해 필요한 'b'는 6개 'c'는 3개이다. 반대로 조합 (bcc, bcc, bcc)의 경우에는 'b' 3개, 'c' 6개가 필요하다.

이는 하나의 단어보드를 기준으로 각각 구해진 필요한 minimum alphabet set을 합친 것 과 같다.

이 아이디어의 시간복잡도는 $$O(N * (2 * 10 + 26)) \approx O(N)$$로 큰 입력에도 효율적으로 문제를 풀수있다.

```cpp
// Block Game
// http://www.usaco.org/index.php?page=viewproblem2&cpid=664

#include <bits/stdc++.h>

using namespace std;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N;
  cin >> N;

  vector<int> ans(26);

  for (int i = 0; i < N; ++i) {
    string a, b;
    cin >> a >> b;

    vector<int> cnt_a(26), cnt_b(26);
    for (auto ch : a) {
      ++cnt_a[ch - 'a'];
    }
    for (auto ch : b) {
      ++cnt_b[ch - 'a'];
    }

    for (int i = 0; i < 26; ++i) {
      ans[i] += max(cnt_a[i], cnt_b[i]);
    }
  }

  for (auto e : ans) {
    cout << e << "\n";
  }

  return 0;
}
```