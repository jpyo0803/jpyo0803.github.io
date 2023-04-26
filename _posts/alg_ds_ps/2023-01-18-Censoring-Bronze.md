---
layout: single
title:  "[USACO] Censoring (Bronze)"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - alg_ds_ps
use_math: true
---

문제: [Censoring (Bronze)](http://www.usaco.org/index.php?page=viewproblem2&cpid=526)

이 문제를 처음 접하자마자 풀기 위해 드는 생각은 문자열(haystack) **S**의 맨 앞부분부터 스캔하여(key) 문자열 **T**와 일치하는 부분문자열을 찾으면 삭제해나가는 방식으로 더 이상 **T**와 매치하는 문자열이 존재하지 않을 때까지 위의 과정을 반복하면 된다.

#### 예제 1

```txt
# 예제 1

S = "whatthemomooofun"
T = "moo"

* () = 문자열 T와 매칭하는 문자열 S의 맨처음 부분 문자열
"whatthemo(moo)ofun"
-> "whatthe(moo)fun" 
-> "whatthefun" 
```

#### 시간복잡도 (Naive way)
그렇다면 위의 방식의 시간복잡도는 어느정도가 될까? 

시간복잡도 계산을 위한 몇가지 사실 (M = S의 길이, N = T의 길이)
1. 최대의 횟수로 매칭하는 부분문자열을 찾는 횟수는 대략 $$\frac{M}{N}$$ 회이다.  
2. 만약 문자열 **S**의 끝부분에서 문자열 **T**와 일치하는 부분문자열을 찾는다면 가장 기본적인 문자열 매칭 방식을 사용하면 $$O(M*N)$$ 시간복잡도가 필요하다. 
3. 만약 문자열 **S**의 문자열 **T**와 일치하는 부분문자열을 문자열 **S**에서 삭제한다고하면 일반적인 문자열 데이터구조 (std::string, STL C++)을 사용한다면 최악의 경우 $$O(M)$$의 시간복잡도가 든다실
위의 열거된 사실들을 종합하여 시간복잡도를 계산해보면 대락적으로 $$O(\frac{M}{N} * M * N * M) \approx O(M^{3})$$ 의 시간복잡도가 됨을 확인 할 수 있다. 

문제에서 주어진 입력의 크기는 최대 $$M=1000000$$ 이 될 수 있음을 고려하면 위의 알고리즘으로는 제한시간내에 풀 수 없음을 알 수 있다. 만약 기본적인 문자열 매칭 방식 $$O(M*N)$$ 대신에 $$O(M + N)$$의 시간복잡도를 갖는 KMP (Knuth-Morris-Partt Algorithm) 문자열 패턴 매칭 알고리즘을 사용한다고해도 제한시간내에 풀 수 없는 것은 마찬가지이다.

#### Solution
그렇다면 이 문제를 제한시간내에 풀기위해 어떻게 접근해야할까? 

생각을 조금 전환하여 우리가 최종적으로 출력하고자하는 문자열 **ans**라는 문자열에 문자열 **S**를 앞에서 시작하여 문자단위로 추가해나가며 매번 문자열 **ans**의 맨 뒤의 부분이 문자열 **T**와 일치하는지 확인해보고 일치한다면 해당 부분을 삭제해나가며 정답을 구하면 어떨까? 
즘
#### 예제 2
```txt
# 예제 2

S = "whatthemomooofun"
T = "moo"

ans = "" # empty string으로 시작
-> ans = "w" # 문자열 S의 맨 처음 문자를 추가
-> ans = "wh" # 문자열 S의 앞에서 두번째 문자를 추가
...
-> ans = "whatthemomoo" # 문자 S[11]을 추가
-> ans = "whatthemo" # 여기서 맨뒤 "moo"가 겹치므로 삭가
-> ans = "whatthemoo" # 문자 S[12]을 추가
-> ans = "whatthe" # 여기서 맨뒤 "moo"가 겹치므로 삭제
-> ans = "whatthef" # 문자 S[13]을 추가
-> ans = "whatthefu" # 문자 S[14]을 추가
-> ans = "whatthefun" # 문자 S[15]을 추가 (완료)
```

#### 시간복잡도 (Efficient way)
위의 알고리즘의 시간복잡도를 구하기 위해 필요한 몇가지 사실을 나열해보면

1. 문자열 **S**를 처음부터 끝까지 한번 스캔하기 위해 필요한 시간복잡도는 $$O(M)$$이다.
2. 문자열 **ans** 맨 뒤에 문자를 하나 추가할때마다 문자열 **T**와 일치하는지 비교하기 위해 필요한 시간복잡도는 $$O(N)$$이다. 

위 두 사실을 종합하면 최종적인 시간복잡도는 O(N * M)이다. 이를 통해 최악의 경우 대략적으로 1억번 (1000000 * 100)의 연산횟수가 필요함을 알 수 있다. 이는 일반적으로 주어지는 시간인 2초에 근접해 아슬아슬하지만 여기 계산에서 고려된 최악의 상황은 실제 절대 일어날 수 없을 것이라 생각한다.

#### Implementation
바로 앞 예제2에서 설명한 알고리즘을 코드로 구현하면 아래와 같이 구현 할 수 있다.
```cpp
// Censoring (Bronze)
// http://www.usaco.org/index.php?page=viewproblem2&cpid=526

#include <bits/stdc++.h>

using namespace std;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  string S, T;
  cin >> S >> T;

  string ans;

  for (auto ch : S) {
    ans.push_back(ch);
    if (ans.length() >= T.length()) {
      if (ans.substr(ans.length() - T.length()) == T) {
        for (int i = 0; i < T.length(); ++i) {
          ans.pop_back();
        }
      }
    }
  }
  cout << ans << "\n";

  return 0;
}
```