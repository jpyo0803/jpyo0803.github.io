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

#### Case 1: ParamType이 Reference이거나 Pointer일때

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

#### Case 2:

