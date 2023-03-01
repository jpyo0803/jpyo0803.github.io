---
layout: single
title:  "[DMOJ] Twenty-four"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - algorithm_ps
use_math: true
---

문제: [Twenty-four](https://dmoj.ca/problem/ccc08s4)

$C (1 \leq C \leq 13)$의 범위를 갖는 숫자 4개가 주어졌을때 연산자 $+$, $-$, $*$,$/$를 사용하여 만들 수 있는 24이하의 결과들 중 최대 값을 찾아야 한다.

숫자 4개의 위치는 마음대로 변경 가능하다 (순열).
또한 숫자간의 연산을 위해 필요한 연산자 3개의 종류도 마음대로 변경 가능하고 중복을 허용한다. (중복 순열)

### Approach 1
모든 경우의 수를 검사하기 앞서 숫자들의 순열과 연산자들의 종류가 이미 정해졌다고 했을때 계산하는 방법에 대해서 먼저 알아보면 좋을 것 같다.

![twenty four img 1](/assets/image/algorithm_ps/twenty_four/twenty_four_img_1.png)
위 그림을 보면 각 숫자들은 앞에서부터 $a$,$b$,$c$,$d$로 표시했고 숫자 사이의 연산자들은 물음표를 포함한 정사각형 박스로 표현하였다. 

숫자 4개의 위치와 연산자 3개의 종류가 이미 정해진 상태에서 앞에서부터 순서대로 계산하지 않아도 된다고하면 가능한 다른 계산 방법은 아래에서 처럼 2가지 Model만 존재하는 것을 확인 할 수 있다.

![twenty four img 2](/assets/image/algorithm_ps/twenty_four/twenty_four_img_2.png)

이제 모든 가능한 경우의 수를 생성해야 한다.

먼저 숫자 $a$,$b$,$c$,$d$로 이루어진 순열을 통해 나가면서 숫자들의 가능한 모든 배치 경우의 수를 아래처럼 생성한다. 이때 시간복잡도는 $O(N!)$이나 숫자가 4개뿐이므로 총 경우의 수는 24이다.

![twenty four img 3](/assets/image/algorithm_ps/twenty_four/twenty_four_img_3.png)

아래 코드는 숫자 4개의 순열을 next_permutation을 활용해 생성하는 예
```cpp
#include <bits/stdc++.h>

using namespace std;

int main() {
  vector<int> nums(4);
  iota(nums.begin(), nums.begin(), 0);

  do {
    // Check
  } while (next_permutation(nums.begin(), nums.end()));

  return 0;
}

```

생성된 각 순자들의 순서 순열마다 연산자들을 정한다. 이때 시간복잡도는 $O(4^N)$이나 결정해야되는 연산자들의 개수는 3개뿐이므로 총 경우의 수는 64.

![twenty four img 4](/assets/image/algorithm_ps/twenty_four/twenty_four_img_4.png)

아래 코드는 연산자들을 각각

$+ \rightarrow 0$

$- \rightarrow 1$

$* \rightarrow 2$

$/ \rightarrow 3$

으로 표현했을때 중복순열을 생성해내는 방법이다.

```cpp
#include <bits/stdc++.h>

using namespace std;

int main() {

  for (int i = 0; i < (1 << (2 * 3)); ++i) {
    vector<int> ops(3);
    for (int j = 0; j < 3; ++j) {
      ops[j] = (i >> (2 * j)) & 0x3;
    }
  }

  return 0;
}
```

마지막으로 숫자들의 위치가 정해지고 연산자 종류들까지 정해졌을때 앞서 소개했던 Model 1과 Model 2를 주어진 숫자들의 배치와 연산자들로 계산해나온 결과를 정답을 구하는데 활용하면 된다.

Model 1과 Model 2를 각각 계산하는데 constant시간이 걸린다고 했을때, 총 시간복잡도는 $O(4! * 4^3) \approx O(1536)$이다. 

#### Implementation
위의 아이디어들을 활용해 아래처럼 구현할 수 있다.
```cpp
// Twenty-four (DMOJ)
// https://dmoj.ca/problem/ccc08s4

#include <bits/stdc++.h>

using namespace std;

int Eval(int a, int b, int op) {
  if (op == 0) {
    return a + b;
  } else if (op == 1) {
    return a - b;
  } else if (op == 2) {
    return a * b;
  } else {
    if (b == 0) return 1e9;
    if (a % b != 0) return 1e9;
    return a / b;
  }
  return 1e9;
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N;
  cin >> N;

  while (N--) {
    int ans = 0;

    vector<int> nums(4);
    for (int i = 0; i < 4; ++i) {
      cin >> nums[i];
    }

    sort(nums.begin(), nums.end());

    do {
      for (int i = 0; i < (1 << (2 * 3)); ++i) {
        vector<int> ops(3);
        for (int j = 0; j < 3; ++j) {
          ops[j] = (i >> (2 * j)) & 0x3;
        }

        bool ok = true;
        int cand = 0;
        cand = Eval(nums[0], nums[1], ops[0]);
        if (cand == 1e9) ok = false;
        cand = Eval(cand, nums[2], ops[1]);
        if (cand == 1e9) ok = false;
        cand = Eval(cand, nums[3], ops[2]);
        if (cand == 1e9) ok = false;
        if (ok) {
          if (cand <= 24) {
            ans = max(ans, cand);
          }
        }
        ok = true;
        int left = Eval(nums[0], nums[1], ops[0]);
        if (left == 1e9) ok = false;
        int right = Eval(nums[2], nums[3], ops[2]);
        if (right == 1e9) ok = false;
        if (ok) {
          cand = Eval(left, right, ops[1]);
          if (cand != 1e9) {
            if (cand <= 24) {
              ans = max(ans, cand);
            }
          }
        }
      }
    } while (next_permutation(nums.begin(), nums.end()));

    cout << ans << "\n";
  }

  return 0;
}
```


