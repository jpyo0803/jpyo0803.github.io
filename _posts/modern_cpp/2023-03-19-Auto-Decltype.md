---
layout: single
title:  "Auto and Decltype Keywords"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - modern_cpp
use_math: true
---

C++11 이후 **auto**와 **decltype** 키워드들이 추가되며 C++는 타입추론 기능은 더 강력해졌다. 

### 'auto' keyword
**auto** 키워드는 컴파일러가 expression을 보고 데이터의 타입을 자동으로 추론하도록 지시한다.


```cpp

auto x = 1234; // x is int
std::cout << typeid(x).name() << std::endl; // prints 'i', which means integer

auto y = 3.14; // y is double
std::cout << typeid(y).name() << std::endl; // prints 'd', which means double

```

타입추론 기능의 광범위한 사용은 우리를 매번 번거롭게 데이터 타입을 명시하는데서 자유롭게 해준다. 하지만 그만큼 눈으로 데이터의 타입을 알아차리기 어렵게 만들기도 한다.

```cpp

int x = 1;
int y = 2;
int z = 3;

int sum = x + y + z;
int mult = x * y * z;

```
만약 위와 같이 **int**에 대한 연산을 **double**로 바꾸어야 한다면 아래처럼 다섯군데를 변경하여야한다.

```cpp

double x = 1.1;
double y = 2.2;
double z = 3.3;

double sum = x + y + z;
double mult = x * y * z;

```

만약 아래 예제처럼 **auto** 키워드를 사용하였다면 이러한 수고로부터 자유로워질 수 있다.

```cpp

auto x = 1.1;
auto y = 2.2;
auto z = 3.3;

auto sum = x + y + z;
auto mult = x * y * z;

```

### 'decltype' keyword

**decltype** 키워드 사용을 통해 특정 데이터의 타입을 추론해 타입 키워드로 사용할 수 있도록 해준다.
```cpp

auto y = 3.14; // y is double, because 'expr' is 3.14, which is double
std::cout << typeid(y).name() << std::endl; // prints 'd', which means double

decltype(y) z = 7; // z is double, because the data type of y is double
std::cout << typeid(z).name() << std::endl; // prints 'd', which means double

```