---
layout: single
title:  "[Algorithm] Sorting Lower Bound"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - algorithm_ps
use_math: true
---

Lower bound of comparison based sorting algorithms

이미 이 세상에는 다양한 비교기반(Comparison-based) 정렬 알고리즘들이 알려져 있다.

Big-O 시간복잡도를 기준으로 비교기반 정렬 알고리즘들을 분류하자면

$O(N^2)$: Bubble sort, Insertion sort, Selection sort, Quick sort, ...
$O(N\log_2N)$: Merge sort, Heap sort

그렇다면 $O(N\log_2N)$ 시간복잡도보다 효율적인 비교기반 정렬 알고리즘은 존재하지 않는 것일까?

결론부터 말하자면 왜 시간복잡도 $O(N\log_2N)$보다 효율적인 비교기반 정렬 알고리즘이 존재할수 없는지 수학적 증명을 통해 보일 수 있다.

먼저 값을 모르는 숫자 3개 $a$, $b$, $c$가 존재한다고 하자. (서로 같지 않다고 가정)
각 숫자의 값에 따라서 6개의 다른 순서로 숫자들을 아래와 같이 나열할 수 있다.

Case 1: $a < b < c$

Case 2: $a < c < b$

Case 3: $b < a < c$

Case 4: $b < c < a$

Case 5: $c < a < b$

Case 6: $c < b < a$

이는 Permutation을 통해 생성할 수 있는 경우의 수의 총 개수이다.

그렇다면 위의 각 경우들을 도출하기 위해 필요한 비교연산 횟수는 얼마일까?

3개의 다른 숫자를 비교해 크기대로 순서를 정하기 위해서는 3개의 비교 연산 종류만 있으면 된다. 

#### The minimum set of required comparisons for 3 distinct number

**1st comparision**: $a<b \rightarrow ?$

**2nd comparision**: $b<c \rightarrow ?$

**3rd comparision**: $a<c \rightarrow ?$ 

ex)
예를들어 $a=1$, $b=2$, $c=3$이라고 해보자.

그렇다면 이는 위에서 나열한 Case들 중 첫번째에 해당한다. 그럼 첫번째 케이스임을 보이기 위해 필요한 비교연산 횟수는 몇일까?

**1st comparision**: $a<b \rightarrow True$

**2nd comparision**: $b<c \rightarrow True$

세번째로 $a<c$를 시도해볼 수 있지만 이를 통해 얻는 추가 정보는 없다.

총 **2번**의 비교연산이 필요하였다.

이번에는 $a < c <b$의 경우를 보자.

**1st comparision**: $a<b \rightarrow True$

**2nd comparision**: $b<c \rightarrow False$ (That is $c<b$)

**3rd comparision**: $a<c \rightarrow True$ 

이번에는 총 3번의 연산이 필요했다.

각 케이스에 대해 표현한 **Comparison tree**는 아래와 같다.

![sorting lower bound img 1](/assets/image/algorithm_ps/sorting_lower_bound/sorting_lower_bound_img_1.png)

우리는 각 결과값들이 Leaf node로서 Comparison tree에 위치한다는 것을 알 수 있다. 

그리고 트리의 높이 $h$는 최대 필요한 비교 연산의 횟수임도 알 수 있다.

그렇다면 $N$개의 숫자들이 존재할때 생성해낼 수 있는 순서의 경우의 수는 $N!$가지가 나옴을 알 수 있다. 각 경우는 Comparison tree에서 leaf node이다.

그리고 $N!$개의 leaf node들을 갖는 Comparision의 높이 $h$는 아래와 같이 표현할 수 있다.

$h=\log_2{N!} = \log_2{(1*2*3*...*N)}=\log_2{1}+\log_2{2}+...+\log_2{N}$

위의 가장 오른쪽에 있는 $N$개의 항 중에 앞에 $N/2$개의 항을 없애고 뒤에 $N/2$항을 $\log_2(N/2)$로 바꾸면 아래와 같은 식을 도출 할 수 있다.


$h= \log_2(1)+...+\log_2(N)$
$\geq\xcancel{\log_2(1)+...+\log_2(N/2)}+\overbrace{\log_2(N/2)+...+\log_2(N/2)}^{N/2}$
$=(N/2)\log_{2}{(N/2)}$

이를 통해 Comparison tree의 높이는 적어도 $(N/2)\log_2(N/2)$을 보일 수 있다. 여기서 트리의 높이는 $N$개의 숫자들을 정렬하기 위해 필요한 비교 연산의 횟수이다.

**즉, 비교기반 정렬 알고리즘의 시간복잡도는 $O(N\log_2{N})$이 최적임을 확인 할 수 있다.**

