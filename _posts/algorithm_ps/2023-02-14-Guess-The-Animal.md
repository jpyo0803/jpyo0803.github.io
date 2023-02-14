---
layout: single
title:  "[USACO] Guess the Animal"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - algorithm_ps
use_math: true
---

문제: [Guess the Animal](http://www.usaco.org/index.php?page=viewproblem2&cpid=893)

답이 될수 있는 동물들의 목록과 각 동물이 가지고 있는 특성이 주어진다. 동물들의 어떤 특성은 공유되어질 수도있고 unique할수도 있다.

문제에서 Elsie는 주어진 동물목록 상에서 Bessie가 마음속으로 정한 동물을 동물들이 가진 다양한 특성을 질문으로하여 "Yes" 또는 "No"를 받아내야한다. 질문을 하는 것은 하나의 동물만이 지금까지의 질문들의 조건을 만족할때 멈추고 마지막으로 남은 동물을 정답으로 외친다. 

그렇다면 Elsie는 최대 몇개의 질문을 할수 있을까?

### Approach 1
가장 간단한 방법은 모든 특성들의 순열을 생성하여 각 케이스에 대해 한마리의 동물이 남았을때 지금까지 했던 질문의 개수를 후보로하여 최대 값을 취하면된다. 

그렇다면 총 가능한 특성의 최대 개수를 짐작해보면 각 동물이 100개의 공유하지 않는 특성들을 보유하고 있고 100마리의 다른 동물이 있다면 최대 10,000개의 특성이 존재한다. 즉, $O(N*K)$. 최악의 경우 $O(100*100) = O(10000)$.

이 방식을 사용하면 순열을 생성($O(N*K)$)하고 각 케이스에 대해 최대 K번 질문을해야한다면 시간복잡도는 $O((N*K)!*K)$로 현실적으로 실행 불가능한 시간복잡도를 갖는다.

즉, 위의 방법을 사용할 수 없다.

### Approach 2
그렇다면 현실적으로 사용가능한 효율적인 알고리즘은 무엇일까?

질문을 해도 동물들이 후보군에서 탈락하지 않고 유지되는 경우는 언제일까? 이 상황은 어떤 동물들이 질문으로 사용한 특성을 공유하는 경우이다.

![guess the animal img 1](/assets/image/algorithm_ps/guess_the_animal/guess_the_animal_img_1.png)
예를들어 위에서처럼 어떤 두 동물(여기서는 Cat과 Dog)이 공유하지 않는 특성인 "bark"을 질문으로 한다면 아래처럼 둘 중하나는 후보군에서 탈락하게 된다.

![guess the animal img 2](/assets/image/algorithm_ps/guess_the_animal/guess_the_animal_img_2.png)
반면에 위에서처럼 어떤 두 동물이 공유하는 특성을 질문으로하면 둘다 생존하게 된다.

![guess the animal img 3](/assets/image/algorithm_ps/guess_the_animal/guess_the_animal_img_3.png)
즉, 최대한 많은 질문을 하고 싶다면 위에서처럼 공유되는 특성들부터 먼저 물어보고 더 이상 공유되는 질문이 존재하지 않게될때 어떤 동물에 고유한 특성을 질문으로하여 최종적으로 한 마리의 동물만 남게하여 알고리즘을 종료한다. 

그렇다면 위에 예시들에서처럼 2마리가 아닌 더 많은 동물들이 존재한다면 어떤 과정을 통해 최대한 많은 질문들을 하면서 최종적으로 한 마리의 동물만 남길 수 있을까?

결론부터 말하자면 두 마리의 동물들을 pair로 묶어 최종으로 남는 두 마리라고 가정하는 것이다. 그리고 두 동물이 공유하고 있는 특성의 개수를 구하고 거기에 1을 더한 값이 후보값이 된다.

$x=the\ number\ of\ shared\ characteristics\ of\ two\ animals$

$candidate_{i}=x_{i}+1, where\ i\ is\ some\ pair$

$answer=max({candidate_{1}, candidate_{2}, ..., candidate_{N*(N-1)/2}})$

### 왜 pair
그렇다면 왜 가능한 모든 부분집합(subset) 대신에 모든 순서쌍(pair)만 검사해도 올바른 정답을 구해낼수있는 것인가?

![guess the animal img 4](/assets/image/algorithm_ps/guess_the_animal/guess_the_animal_img_4.png)
위 예시를 보면 Dog를 기준으로하고 Cat을 비교대상으로 선정했을때 특성 "D"를 질문하는 경우와 동일하게 Dog를 기준으로하고 이번에는 Owl을 비교대상으로 선정하고 특성 "D"를 질문하는 경우가 결과적으로는 동일함을 알 수 있다. 

즉, Dog, Cat, 그리고 Owl 모두를 포함한 부분집합인 상황을 고려하는 것과 같은 결과를 보인다. 물론 부분집합으로 보고 처리했을때 한번만 계산해 보아도 되는 케이스를 순서쌍을 기준으로 계산한다면 똑같은 결과를 두번 마주치게 된다.

하지만 총 $N$ 종류의 동물이 존재할때 고려해야되는 부분집합의 개수는 $O(2^N)$이다 반면에 순서쌍으로 고려한다면 $O(N^2)$밖에 안된다.

$O(2^N) >>> O(N^2)$ 이므로 훨씬더 효율적이다. 

### Implementation
Approach2에서의 아이디어를 구현하면 아래와 같다.

```cpp
// Guess the Animal (Bronze)
// http://www.usaco.org/index.php?page=viewproblem2&cpid=893

#include <bits/stdc++.h>

using namespace std;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  cout.tie(nullptr);

  int N;
  cin >> N;

  vector<set<string>> animals(N);

  for (int i = 0; i < N; ++i) {
    string A;
    int K;
    cin >> A >> K;

    for (int j = 0; j < K; ++j) {
      string B;
      cin >> B;
      animals[i].insert(B);
    }
  }

  int ans = 0;
  for (int i = 0; i < N - 1; ++i) {
    for (int j = i + 1; j < N; ++j) {  // ordered pair of cows
      int sc = 0;
      for (auto& a : animals[i]) {
        if (animals[j].find(a) != animals[j].end()) {
          sc++;
        }
      }
      ans = max(ans, sc);
    }
  }

  cout << ans + 1 << "\n";

  return 0;
}
```