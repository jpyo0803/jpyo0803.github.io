---
layout: single
title:  "Fibonacci Sequence"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - math_for_cs
use_math: true
---

컴퓨터 과학을 공부하다보면 피보나치 수열과 관련된 내용이 자주 등장하는 것을 확인 할 수 있다. 

마침 이번에 듣는 수업에서도 피보나치 수열에 대해 심도있게 다루기에 공부할 겸 이 포스트를 쓰기로 했다.

참고로 저는 수학 전공자가 아니기 때문에 앞으로 등장할 증명해나가는 과정중에 포함된 논리나 엄밀하지 않거나 틀릴 수도 있다. 

(단순히 스스로를 위한 공부 및 정리 목적으로 포스팅을하는 것이기 때문에 너그럽게 눈감아 주시길 바란다...) 

### Fibonacci Sequence

$ \begin{aligned}F_{0}=0,\ F_{1}=1,\ F_{2}=1,\ F_{3}=2,\ F_{4}=3,\ F_{5}=5,\ ... 
   \end{aligned}
$

피보나치 수는 위와 같은 수열을 갖는 수이다. 

자세히 들여다보면 알겠지만 첫 두 피보나치 수를 제외하고는 특정 위치의 피보나치수는 자신의 앞 두개의 피보나치 수를 더한 값이다.

```math
\begin{aligned}
F_{n}=
\begin{cases}
  0, & \text{if n=0}\\
  1, & \text{if n=1}\\
  F_{n-1}+F_{n-2}, & \text{otherwise}\\
\end{cases}
\end{aligned}
```

피보나치 수열의 특징 중 하나는 매우 큰 $n$에 대해 
```math
\begin{aligned}
\lim_{n\to\infty} \frac{F_{n}}{F_{n-1}}=\frac{1+\sqrt{5}}{2} \approx 1.618
\end{aligned}
```
위와 같이 황금비(Golden ratio)을 갖는다는 점이다.

이러한 피보나치 수열의 황금비는 자연 곳곳에서 발견된다고 한다.

### Computing $n$-th Fibonacci Number

##### Recursive Method
특정 $n$번째 피보나치 수를 구하는 방법은 무엇이 있을까?

가장 쉬운 방법은 이미 주어진 점화식을 활용해 재귀적으로 구하는 것이다.
```cpp
int RecursiveFib(int n) {
  if (n == 0 || n == 1) {
    return n;
  }
  return RecursiveFib(n - 1) + RecursiveFib(n - 2)
}
```
하지만 위의 방식 (RecursiveFib)는 매우 큰 시간복잡도를 갖기에 $n$이 조금만 커져도 $F_{n}$을 효율적으로 구하지 못하게 된다.

그렇다면 RecursiveFib방식의 시간복잡도를 구해보자.

먼저 피보나치 수열의 Recurrence relation은 아래와 같다.
$$
T(n)=T(n-1)+T(n-2)+\mathcal{O}(1)
$$
위의 점화식을 통해 하나의 부분문제(sub problem)을 해결하기 위해 시간복잡도 $\mathcal{O}(1)$의 비용이 드는 것을 확인 할 수 있다. 

하나의 부분문제를 풀기위한 비용이 상수값(constant)이기에 문제 전체를 풀기위한 비용은 단순히 해당 문제를 구성하고 있는 부분문제의 개수를 구하면 된다.

![fibonacci sequence img 1](/assets/image/math_for_cs/fibonacci_sequence/fibonacci_sequence_img_1.png)
이해를 돕기 위해 위의 그림을 보자. 

먼저 $S(F_{n})$를 $F_{n}$를 구하기 위해 풀어야할 부분문제의 개수라고 하자.

$$
S(F_{2})=3\newline
S(F_{3})=5\newline
S(F_{4})=9\newline
$$
위의 그림을 보면 부분문제의 개수는 아래와 같은 점화식을 갖는 것을 확인 할 수 있다.

$$
  S(F_{n})=\begin{cases}
    1, & \text{if n=0 or n=1}\\
    S(F_{n-1})+S(F_{n-2}) + 1, & \text{otherwise}\\
  \end{cases}
$$

그렇다면 위의 점화식을 Closed Form으로 나타내보자.

$$
S(F_{n})=S(F_{n-1})+S(F_{n-2})+1>S(F_{n-2})+S(F_{n-2})\newline
=2S(F_{n-2})>2(2S(F_{n-4}))>...>2^{n/2}*S(F_{0})\newline
=2^{n/2}
$$
최종적으로 Closed Form은 아래와 같음을 확인할 수 있다.
$$
S(F_{n})>2^{n/2}=\mathcal{\Omega}(2^{n})
$$

즉 $T(n)$을 풀기 위해 적어도 $2^{n}$개의 부분문제를 풀어야하고 총 비용은 $\mathcal{O}(2^{n})$이다.

##### Recursive Method with Dynamic Programming
앞서 보았던 recursion tree를 확인해보면 같은 부분문제를 불필요하게 반복적으로 계산하고 있음을 확인할 수 있다. 이는 Dynamic Programming을 통해 각 부분문제를 한번만 계산함으로서 시간복잡도를 $\mathcal{O}(n)$으로 줄일 수 있다. (코드는 생략)

##### Iterative Method
$n$번째 피보나치 수를 구하기 위해 우리가 필요한 정보는 $n-1$번째와 $n-2$번째 피보나치 수의 값이다. 이러한 반복 알고리즘을 통해 $n$번째 피보나치 수를 구하기 위해 드는 비용은 $\mathcal{O}(n)$이다. 공간복잡도는 바로 직전 두개의 피보나치 수들만 기억하면 되기에 $\mathcal{O}(1)$이다. (코드는 생략)

##### Repeated Squaring Method
앞서 Dynamic programming과 Iterative method를 통해 시간복잡도를 $\mathcal{O}(n)$까지 linear한 단계까지 줄일 수 있었다. 

더 빠른 방법은 없을까? 

다행이도(?) $n$번째 피보나치 수를 구하는데 $\mathcal{O}(\log{n})$의 시간복잡도를 갖는 알고리즘이 있다.

$$
  \begin{pmatrix} 
   F_{n+1} & F_{n}  \\
   F_{n} & F_{n-1}  \\
   \end{pmatrix}=
  {\begin{pmatrix} 
   1 & 1  \\
   1 & 0  \\
   \end{pmatrix}}^{n}
$$

신기하게도 위의 식을 통해 $n$번째 피보나치 수를 $\mathcal{O}(\log_2{n})$에 구할 수 있다. (행렬의 제곱이 로그 시간에 풀리기 때문에) (코드는 생략)

##### Closed Form of $n$-th Fibonacci Number 
앞서 소개한 방법들은 여러번의 계산을 통해 $n$번째 피보나치 수를 구할 수 있었다.

상수 시간 $\mathcal{O}(1)$에 $n$번째 피보나치 수를 구하는 방법은 없을까? 

$F_{n}$을 상수시간에 구해주는 **Binet's Formula**가 존재한다.

$$
F_{n}=\frac{1}{\sqrt{5}}({(\frac{1+\sqrt{5}}{2})}^{n}-{(\frac{1-\sqrt{5}}{2})}^{n})
$$

Binet 공식을 유도하기 위해서 몇가지 보조정리(Lemma)가 선행되어야 한다.

**Lemma 1**: Let $ E_{n} = F_{n}-(\frac{1+\sqrt{5}}{2})F_{n-1} $, then $E_{n}=(\frac{1-\sqrt{5}}{2})^{n-1}$

먼저 우리는 $F_{n}$이 대략적으로 $F_{n-1}$에 황금비를 곱해 구해질 수 있음을 이미 알고 있다.
$$
F_{n} \approx (\frac{1+\sqrt{5}}{2})F_{n-1}
$$

즉, $\approx$를 기준으로 왼쪽과 오른쪽은 정확하게는 같지 않기에 어느정도의 error가 존재함을 알 수 있다.

$$
E_{n}=F_{n}-(\frac{1+\sqrt{5}}{2})F_{n-1}
$$

이제 $E_{n}=(\frac{1-\sqrt{5}}{2})^{n-1}$ 임을 보이자.

$$
E_{n}=F_{n}-(\frac{1+\sqrt{5}}{2})F_{n-1}=F_{n-1}+F_{n-2}-(\frac{1+\sqrt{5}}{2})F_{n-1}\newline
=F_{n-1}(1-\frac{1+\sqrt{5}}{2})+F_{n-2}=F_{n-1}(\frac{1-\sqrt{5}}{2})+F_{n-2}\newline
=(\frac{1-\sqrt{5}}{2})(F_{n-1}+(\frac{2}{1-\sqrt{5}})F_{n-2})\newline
=(\frac{1-\sqrt{5}}{2})(F_{n-1}+(\frac{2}{1-\sqrt{5}})(\frac{1+\sqrt{5}}{1+\sqrt{5}})F_{n-2})\newline
=(\frac{1-\sqrt{5}}{2})(F_{n-1}-(\frac{1+\sqrt{5}}{2})F_{n-2})
$$
그러면 오른쪽에서 $E_{n-1}$를 찾을 수 있다.

$$
E_{n-1}=F_{n-1}-(\frac{1+\sqrt{5}}{2})F_{n-2}
$$
즉 아래와 같은 점화식을 갖게된다.
$$
E_{n}=(\frac{1-\sqrt{5}}{2})E_{n-1}
$$
이를 일반항으로 표현하면 아래처럼 쓸수 있다.
$$
E_{n}=(\frac{1-\sqrt{5}}{2})^{n-1}E_{1}\newline
=(\frac{1-\sqrt{5}}{2})^{n-1}(F_{1}-(\frac{1+\sqrt{5}}{2})F_{0})=(\frac{1-\sqrt{5}}{2})^{n-1}
$$
즉, 보조정리1이 증명된다.
$$
E_{n}=(\frac{1-\sqrt{5}}{2})^{n-1}\ \square
$$

**Lemma 2**: Each term of the Fibonacci sequence is the sum of a finite geometric series with first term $(\frac{1+\sqrt{5}}{2})^{n-1}$ and ratio $\frac{1-\sqrt{5}}{1+\sqrt{5}}$

먼저 **보조정리1**로부터 $F_{n}$을 아래와 같이 쓸수 있다.
$$
F_{n}=(\frac{1+\sqrt{5}}{2})F_{n-1}+E_{n}\newline
F_{n}=(\frac{1+\sqrt{5}}{2})F_{n-1}+(\frac{1-\sqrt{5}}{2})^{n-1}
$$
위의 점화식을 통해 $F_{n-1}$을 $F_{n-2}$를 포함한 식으로 풀어낼 수 있다.

$$
F_{n}=(\frac{1+\sqrt{5}}{2})\lbrack(\frac{1+\sqrt{5}}{2})F_{n-2}+(\frac{1-\sqrt{5}}{2})^{n-2}\rbrack+(\frac{1-\sqrt{5}}{2})^{n-1}
$$
이러한 작업을 $F_{1}$이 나타날때까지 계속하면 아래와 같은 식을 얻을 수 있다.

$$
F_{n}=(\frac{1+\sqrt{5}}{2})^{n-1}+(\frac{1+\sqrt{5}}{2})^{n-2}(\frac{1-\sqrt{5}}{2})+...+(\frac{1+\sqrt{5}}{2})(\frac{1-\sqrt{5}}{2})^{n-2}+(\frac{1-\sqrt{5}}{2})^{n-1}
$$

위를 잘 관찰해보면 $F_{n}$는 첫번째 항이 아래와 같고
$$
a_{0}=(\frac{1+\sqrt{5}}{2})^{n-1}
$$
공비가 아래와 같은 등비수열의 합임을 확인 할 수 있다.
$$
\gamma=\frac{(\frac{1-\sqrt{5}}{2})}{(\frac{1+\sqrt{5}}{2})}=\frac{1-\sqrt{5}}{1+\sqrt{5}}
$$
즉, 
$$
F_{n}=\sum_{k=0}^{n-1}(\frac{1-\sqrt{5}}{1+\sqrt{5}})^{k}(\frac{1+\sqrt{5}}{2})^{n-1}\ \square
$$

**Theorem**: if $F_{n}$ is the $n$-th term of a Fibonacci sequence, then 
$$
F_{n}=\frac{1}{\sqrt{5}}({(\frac{1+\sqrt{5}}{2})}^{n}-{(\frac{1-\sqrt{5}}{2})}^{n})
$$

우리는 **보조정리2**로부터 $F_{n}$이 아래에서처럼 등비수열의 합으로 표현될 수 있음을 확인하였다.
$$
F_{n}=\sum_{k=0}^{n-1}(\frac{1-\sqrt{5}}{1+\sqrt{5}})^{k}(\frac{1+\sqrt{5}}{2})^{n-1}
$$

등비수열의 합은 아래처럼 유도된다.
$$
S_{n}=a_{0}+a_{0}\gamma+a_{0}\gamma^{2}+...+a_{0}\gamma^{n-1}\newline
-(\gamma S_{n})=-(a_{0}\gamma+a_{0}\gamma+a_{2}\gamma^{3}+...+a_{0}\gamma^{n})
$$
$$
S_{n}(1-\gamma)=a_{0}(1-\gamma^{n})\newline
S_{n}=\frac{a_{0}(1-\gamma^{n})}{1-\gamma}
$$

우리의 경우에 위의 공식을 사용해 대입해보면
$$
a_{0}=(\frac{1+\sqrt{5}}{2})^{n-1}\newline
\gamma=\frac{1-\sqrt{5}}{1+\sqrt{5}}\newline
$$
$$
F_{n}=(\frac{1+\sqrt{5}}{2})^{n-1}\frac{(1-(\frac{1-\sqrt{5}}{1+\sqrt{5}})^{n})}{(1-(\frac{1-\sqrt{5}}{1+\sqrt{5}}))}=(\frac{1+\sqrt{5}}{2})^{n-1}\frac{(1-(\frac{1-\sqrt{5}}{1+\sqrt{5}})^{n})}{(\frac{2\sqrt{5}}{1+\sqrt{5}})}\newline
=(\frac{1+\sqrt{5}}{2})^{n-1}\frac{(1-(\frac{1-\sqrt{5}}{1+\sqrt{5}})^{n})}{(\frac{2\sqrt{5}}{1+\sqrt{5}})}\newline
=\frac{(\frac{1+\sqrt{5}}{2})^{n-1}-(\frac{1-\sqrt{5}}{1+\sqrt{5}})(\frac{1-\sqrt{5}}{2})^{n-1}}{(\frac{2\sqrt{5}}{1+\sqrt{5}})}\newline
=(\frac{1+\sqrt{5}}{2\sqrt{5}})[(\frac{1+\sqrt{5}}{2})^{n-1}-(\frac{1-\sqrt{5}}{1+\sqrt{5}})(\frac{1-\sqrt{5}}{2})^{n-1}]\newline
=\frac{1}{\sqrt{5}}[(\frac{1+\sqrt{5}}{2})^{n}-(\frac{1-\sqrt{5}}{2})^{n}]
\\~\\
F_{n}=\frac{1}{\sqrt{5}}[(\frac{1+\sqrt{5}}{2})^{n}-(\frac{1-\sqrt{5}}{2})^{n}]\ \square
$$

이렇게 **Binet's Formula**를 유도할 수 있다.

### Comment
이번 포스팅에서는 $n$번째 피보나치 수 $F_{n}$을 구하는 다양한 방법에 대해 알아보았다. 

다음 포스팅에서는 피보나치 수열와 관련된 더 많은 흥미로운 특성들을 탐구해보겠다. 

### Reference
[1] http://www.milefoot.com/math/discrete/sequences/binetformula.htm