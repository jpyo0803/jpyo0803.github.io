---
layout: single
title:  "Growth of Functions"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - math_for_cs
use_math: true
---

알고리즘의 점근적 효율성(asymptotic efficiency)를 측정하기 위해 일반적으로 $$\mathcal{O}$$, $$\Omega$$, $$\Theta$$ 등과 같은 기호들이 사용된다. 요즘들어 이러한 기호들을 제대로 이해하지 못하고 사용하는 것 같아 다시 공부할겸 포스팅에 정리해본다.

### $$\Theta$$-notation
쎄타 표기법이라고 부른다.

삽입정렬(Insertion sort)의 worst-case running time은 $$T(n)=\Theta(n^{2})$$로 알려져있다. 

이것이 무엇을 의미하는지 알아보자. 

먼저 $$\Theta(g(n))$$은 $$g(n)$$와 같은 class를 갖는 함수들의 집합이다.

예를들어 $$g(n)=n^{2}$$이라고하면 아래와 같이 표현할 수 있다. 

$$
\begin{aligned}
\Theta(n^{2})=\{\forall c \in \mathbb{R^{+}}\ : c \cdot n^{2} \}
\end{aligned}
$$

그렇다면 아래의 식은 어떤 의미를 갖을까?

$$
\begin{aligned}
f(n)=\Theta(g(n))
\end{aligned}
$$

이라고 하면 아래의 식을 만족하는 어떠한 양수 $$c_{1}$$, $$c_{2}$$, $$n_{0}$$가 존재한다는 것이다.

다시말해 함수 $$f(n)$$가 $$g(n)$$ 함수 class 집합에 속한다는 것이다.

$$
\begin{aligned}
0 \leq c_{1}g(n) \leq f(n) \leq c_{2}g(n)\ \ \ \ \  \forall \  n \geq n_{0}
\end{aligned}
$$

간단하게 어떤 알고리즘의 running time이 $$f(n)=\frac{1}{2}n^{2}-3n=\Theta(n^2)$$와 같다고 하면 아래와 같이 표현할 수 있다. 

$$
\begin{equation}
\begin{aligned}
c_{1}n^{2} \leq \frac{1}{2}n^{2}-3n \leq c_{2}n^2
\end{aligned}
\end{equation}
$$

위의 식을 전체적으로 $$n^{2}$$으로 나누면 아래처럼 표현할 수 있다. 

$$
\begin{aligned}
c_{1} \leq \frac{1}{2}-\frac{3}{n} \leq c_{2}
\end{aligned}
$$

그렇다면 위의 조건을 만족하는 $$c_{1}$$, $$c_{2}$$, $$n_{0}$$이 존재할까?

먼저 앞서 정의에서 주어진대로 $$n \geq 1$$라고하면 $$c_{2} \geq 1/2$$이기만하면 오른쪽 부등식을 항상 만족한다.

반면에 왼쪽 부등식의 경우 정의에서 주어진대로 $$c_{1} \geq 1$$을 만족하기 위해서 $$n \geq 7$$이어야하고 $$n=7$$일때 $$c_{1}\leq {1/14}$$이다. 

$$c_{1}=1/14$$, $$c_{2}=1/2$$, $$n_{0}=7$$일때 식 (1)을 만족하는 것을 확인 할 수 있다. 

즉, $$f(n)=\frac{1}{2}n^{2}-3n=\Theta(n^2)$$이다.

이를 그래프로 표현하면 아래와 같다.
![growth of functions img 1](/assets/image/math_for_cs/growth_of_functions/growth_of_functions_img_1.png)

위의 그래프를 보면 $$f_{n}$$이 $$c_{1}g(n)$$과 $$c_{2}g(n)$$사이에 샌드위치처럼 끼여있는 것을 확인 할 수 있다. 
이는 함수 $$f_{n}$$가 어떠한 constant factor이내로 함수 $$g(n)$$와 사실상 같다고도 표현할 수 있고 이를 교과서에서는 "$$g(n)$$ is an **asymptotically tight bound** for $$f(n)$$"이라고 표현한다.

이번에는 반대로 $$6n^{3}\not=\Theta(n^{2})$$임을 보여보자.

$$
\begin{aligned}
c_{1}n^{2} \leq 6n^{3} \leq c_{2}n^{2}
\end{aligned}
$$

여기서 오른쪽 부등호의 오른쪽변을 $$n^{2}$$으로 나누면 아래와 같다.
$$
\begin{aligned}
6n \leq c_{2}
\end{aligned}
$$

여기서 $$c_{2}$$는 상수이고 임의의 큰 $$n$$보다도 항상 큰 상수는 존재할 수 없기에 모순이 발생한다.

즉, $$6n^{3} \not= \Theta(n^{2})$$ 이다 (Proof by contradiction).

또한 실제 $$f(n)=\Theta(g(n))$$임을 보일때 $$n$$이 조금만 커져도 highest term이 lower term들을 dominate하기때문에 lower term들은 무시해도 결과에는 영향을 주지 않는다. 

### $$\mathcal{O}$$-notation
빅오 표기법이라고 부른다.

$$
\begin{aligned}
f(n)=\mathcal{O}(g(n))
\end{aligned}
$$

이라고 하면 아래의 식을 만족하는 어떠한 양수 $$c$$, $$n_{0}$$가 존재한다는 것이다.

$$
\begin{aligned}
 f(n) \leq cg(n)\ \ \ \ \  \forall  n \geq n_{0}
\end{aligned}
$$

$$\Theta$$와 달리 $$\mathcal{O}$$는 **asymptotic upper bound**에만 관심을 둔다는 것이다. ($$\Theta$$는 **asymptotic lower / upper bounds** 둘다에 관심을 두었음)

![growth of functions img 2](/assets/image/math_for_cs/growth_of_functions/growth_of_functions_img_2.png)

### $$\Omega$$-notation
오메가 표기법이라고 부른다.

$$\mathcal{O}$$의 정반대이다. 

$$
\begin{aligned}
f(n)=\Omega(g(n))
\end{aligned}
$$

이라고 하면 아래의 식을 만족하는 어떠한 양수 $$c$$, $$n_{0}$$가 존재한다는 것이다.

$$
\begin{aligned}
0 \leq cg(n) \leq f(n) \ \ \ \ \  \forall n \geq n_{0}
\end{aligned}
$$

![growth of functions img 3](/assets/image/math_for_cs/growth_of_functions/growth_of_functions_img_3.png)

### Then, $$\Omega=$$best case running time? and $$\mathcal{O}=$$worst case running time?
내가 가장 헷갈렸던 점이다. 

어떤 알고리즘의 최선의 경우에는 무조건 $$\Omega$$를 표기법을 사용하고 최악의 경우에는 $$\mathcal{O}$$ 표기법을 사용해야만 하는가?

결론부터 말하자면 아니다. 둘은 직접적인 관련이 없다. (지금까지 이해하기론...)

이해를 돕기위해 간단한 상황을 들어보자. 

삽입정렬(Insertion sort)의 최악의 경우 대략적으로 $$f(n)=10n^{2}$$ operations가 필요하다고 해보자. 
그렇다면 당연히 $$\mathcal{O}(n^2)$$로 표기해도 된다. 또한 적어도 $$10n^2$$만큼의 operation이 필요하기 때문에 $$\Omega(n^{2})$$라고 표기해도 틀리지 않기 때문에 삽입정렬의 최악의 시간복잡도는 $$\Theta(n^{2})$$이라고 해도 된다.

만약에 최악의 경우가 아닌 삽입정렬 모든 경우의 시간복잡도를 물어본다면 $$\Theta(n^{2})$$라고 말할 수 없다. 왜냐하면 최선의 경우 $$\Omega(n^{2})$$만큼의 시간복잡도를 가지기 때문이다. 

다음부터는 헷갈리지 않도록 주의해야겠다.

### Reference
[1] Introduction to Algorithm (3rd Edition) 