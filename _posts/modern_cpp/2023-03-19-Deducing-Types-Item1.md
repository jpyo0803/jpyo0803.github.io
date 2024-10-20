---
layout: single
title:  "[Deducing Types] Template Type Deduction"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - modern_cpp
use_math: true
---

### Template Type Deduction

C++에서 Template 함수는 일반적으로 아래와 같은 구조를 갖고 있다.


```cpp
template<typename T>
void f(ParamType param);

f(expr)
```

컴파일시에 컴파일러는 **T**와 **ParamType**을 결정해야한다.


```cpp
template<typename T>
void f1(T param); // pass by value

template<typename T>
void f2(T& param); // reference

template<typename T>
void f3(const T& param); // const reference

template<typename T>
void f4(T&& param); // universal reference

template<typename T>
void f5(T* param); // pointer

template<typename T>
void f6(const T* param); // pointer

f(expr) 
```
**ParamType**은 다양한 수식을 갖을 수 있다. 

위에 예시 코드에서처럼 **ParamType**을 어떻게 수식하느냐에 따라 다양한 조합이 존재한다. 

```cpp
template<typename T>
void f(const T& param);

int x = 7;
const int cx = x;
const int& crx = x;

f(x);
f(cx);
f(crx);
```
더 나아가 위 예시에서 처럼 Template 함수에 전달되는 argument의 타입에 따라서도 다양한 조합이 존재한다.

우리가 궁금한 것은 이렇게 다양한 상황에서 컴파일러가 컴파일 과정중에 외부에서 함수로 전달된 **argument의 타입**과 함수 내부에서 **ParamType의 정의**에 따라 최종적으로 어떠한 결과물을 내놓을지이다.

```cpp

template<typename T>
void f(T param); // What is deduced type of T?

int x = 7;
const int& crx = x;

```
위의 상황에서 컴파일러가 최종적으로 추론한 ParamType **T**는 무엇일까?

crx를 따라 const int&가 될까?

아니면

const와 reference 수식을 떼어내고 그냥 **T**를 따라 int일까? 

이를 명확하게 구분하기 위한 3가지 케이스가 존재한다.

#### Case 1: ParamType이 Reference이거나 Pointer인 경우

- Rules
  1. **expr**를 **reference**가 수식한다면 무시한다. 
  2. **expr**의 타입과 **ParamType**의 패턴을 매칭한다.

##### Examples
```cpp
template<typename T>
void f(T& param); // reference

int x = 7;
const int cx = x;
const int& crx = x;

f(x); 
// expr 'x' is not adorned with a reference in the first place,
// so T is deduced to be 'int' and ParamType to be 'int&'

f(cx); 
// again expr 'x' is not adorned with a reference,
// T is deduced to be 'const int' and ParamType to be 'const int&'

f(crx); 
// The reference adornment is ignored, 
// T is deduced to be 'const int' and ParamType to be 'const int&'
```

아래의 경우는 ParamType이 const T&인 경우이다.
```cpp

template<typename T>
void f(const T& param); // const reference

int x = 7;
const int cx = x;
const int& crx = x;

f(x); 
// expr 'x' is not adorned with a reference in the first place, 
// so T is deduced to be 'int' and ParamType to be 'const int&'

f(cx); 
// again expr 'x' is not adorned with a reference. 
// However, in this case, ParamType already has 'const',
// so T does not need to include 'const' keyword. 
// T is deduced to be 'int'. ParamType is deduced to be 'const int&'

f(crx); 
// The reference adornment is ignored, 
// T is deduced to be 'int' and ParamType to be 'const int&'

```

아래의 경우는 ParamType이 T*인 경우이다.

```cpp

template<typename T>
void f(T* param);

template<typename T>
void f2(const T* param);

int x = 7; 
const int cx = x;
const int* cpx = &x;

f(&x);
// T is deduced to be 'int', ParamType to be 'int*'

f(&cx);
// T is deduced to be 'const int', ParamType to be 'const int*'

f(cpx);
// T is deduced to be 'const int', ParamType to be 'const int*'

f2(cpx);
// T is deduced to be 'int', ParamType to be 'const int*'

```

Case 1의 경우 규칙은 생각보다 간단한 것같다. 단순히 reference가 존재한다면 무시한다. 그런후 **ParamType**에 없지만 **expr**에는 존재하는 수식어와 데이터 타입만 사용해 **T**를 결정한다.

#### Case 2: ParamType이 Universal Reference인 경우

- Rules
  1. **expr**를 **lvalue**라면 **T** 무조건 **lvalue reference**로 추론된다. 이 경우에만 **유일**하게 **T**가 reference를 수식한다.
  2. **expr**가 **rvalue**라면 일반적인 규칙을 따른다. 

```cpp

template<typename T>
void f(T&& param); // T&& is universal reference

int x = 7;
const int cx = x;
const int& crx = x;

f(x);
// x is int and lvalue, T is deduced to be 'int&',
// ParamType is deduced to be 'int&'

f(cx);
// cx is const int and lvalue, T is deduced to be 'const int&',
// ParamType is deduced to be 'const int&'

f(crx);
// cx is const int& and lvalue, T is deduced to be 'const int&',
// ParamType is deduced to be 'const int&'

f(99);
// 99 is int and rvalue. Thus, we adhere the normal rules. 
// T is deduced to be 'int', ParamType to be 'int&&'

```

#### Case 3: ParamType이 Pointer도 Reference도 아닐때

- Rules
  1. **expr**가 reference라면 이를 무시한다.
  2. **expr**가 const나 volatile을 수식한다면 이것들도 무시한다.

```cpp

template<typename T>
void f(T param);

int x = 7;
const int cx = x;
const int& crx = x;

f(x);
// x's type is 'int'
// neither adorned by reference nor const or volatile 
// T is deduced to be 'int'
// ParamType is also deduced to be 'int'

f(cx);
// cx's type is 'const int',
// After ignoring 'const', T is deduced to be 'int'
// ParamType is deduced to be 'int'

f(crx);
// Ignore both the reference and const adornments
// T is deduced to be 'int',
// ParamType is deduced to be 'int'

```

Case 3에서 함수호출과 같이 pass되는 argument들은 함수 내부에서는 함수 외부에서의 object와는 관련없는 새롭게 생성된 copy이기 때문에 함수 내부에서 해당 copy를 변경하더라도 외부에서는 영향을 받지 않는다. 즉, 어차피 애초에 reference로 공유되어지는 object가 아닌 독립적인 copy이기때문에 실수로 내용물을 변경하는 것을 방지하기 위해 일반적으로 사용되는 const 키워드를 굳이 붙이지 않아도된다.

다른 상황으로 함수 호출시 argument가 const object를 가르키는 const pointer타입이면 어떻게 될까? 

참고로, const char* const와 같은 데이터 타입에서 char\*의 앞에 붙은 const는 포인터가 가르키고 있는 object의 내용물을 해당 포인터를 통해서는 변경하지 못하도록 하겠다는 의미이고, char\* 뒤에 붙은 


### TODO
Currently working on case 3