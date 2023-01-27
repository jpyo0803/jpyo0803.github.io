---
layout: single
title:  "[Algorithm] Rectangle Geometry"
author_profile: true
sidebar:
    nav: "sidebar-category"
categories:
  - algorithm_ps
use_math: true
---

## Rectangle Geometry
![rectangle_geometry_img_1](/assets/image/algorithm_ps/rectangle_geometry/rectangle_geometry_img_1.png)

### 평면상의 축들(x-axis와 y-axis)과 평행한 직사각형을 2개의 점으로 나타내는 법
![rectangle_geometry_img_2](/assets/image/algorithm_ps/rectangle_geometry/rectangle_geometry_img_2.png)

두 점 $tr$과 $bl$로 **평면의 축들과 면이 평행한** 직사각형을 표현 가능하다.

```cpp

struct Point {
  int x, y;
};

class Rectangle {
 public:
  Rectangle(Point tr, Point bl) : tr_(tr), bl_(bl) {}

  Point tr_, bl_;
};

```

### 두개의 직사각형들이 겹치는지 확인하는 방법
두 직사각형이 겹치지 않는 경우는 두 가지가 있다.
여기서 직사각형 A가 기준이되는 직사각형이라고 하자.

![rectangle_geometry_img_3](/assets/image/algorithm_ps/rectangle_geometry/rectangle_geometry_img_3.png)
1. 기준이되는 직사각형 A보다 B가 위나 아래에 있을때. 즉, $tr_{B}.y \leq bl_{A}.y$ or $tr_{A}.y \leq bl_{B}.y$

![rectangle_geometry_img_4](/assets/image/algorithm_ps/rectangle_geometry/rectangle_geometry_img_4.png)
2. 기준이되는 직사각형 A보다 B가 왼쪽이나 오른쪽에 있을때. 즉, $tr_{A}.x \leq bl_{B}.x$ or $tr_{B}.x \leq bl_{A}.x$

즉, 위의 경우에 해당하지 않는 그 외의 모든 상황은 직사각형들이 겹침을 의미한다.

```cpp

bool DoRectanglesIntersect(const Rectangle& A, const Rectangle& B) {
  // Case 1
  if (A.tr_.y <= B.bl_.y || B.tr_.y <= A.bl_.y) return false;
  // Case 2
  if (A.tr_.x <= B.bl_.x || B.tr_.x <= A.bl_.x) return false;
  // Otherwise
  return true;
}

```

### 직사각형의 넓이를 구하는 방법

![rectangle_geometry_img_5](/assets/image/algorithm_ps/rectangle_geometry/rectangle_geometry_img_5.png)


### 두개의 직사각형들의 겹치는 영역의 넓이 구하는 방법



두개의 직사각형들의 겹치는 영역(만약 겹치는 부분이 존재한다면)은 또 다른 직사각형이다. 

이는 아래의 그림에서 살구색으로 칠해진 직사각형과 같다.

살구색 직사각형또한 두개의 점들($tr_C$와 $bl_C$)으로 나타낼 수 있다. 

![rectangle_geometry_img_6](/assets/image/algorithm_ps/rectangle_geometry/rectangle_geometry_img_6.png)

그렇다면 $tr_C.x$, $tr_C.y$, $bl_C.x$, $bl_C.y$를 어떻게 구할 수 있을까?

조금 관찰해보면 이는 위의 식을 통해 각각 구할 수 있는 것을 확인할 수 있다.

최종적으로 $tr_C$와 $bl_C$를 안다면 겹치는 영역(살구색 직사각형)의 넓이는 앞서 소개했던 방식으로 구하면 된다.

![rectangle_geometry_img_7](/assets/image/algorithm_ps/rectangle_geometry/rectangle_geometry_img_7.png)

다른 상황에서도 똑같은 알고리즘을 적용해 $tr_C$와 $bl_C$를 구할 수 있을을 확인할 수 있다..

![rectangle_geometry_img_8](/assets/image/algorithm_ps/rectangle_geometry/rectangle_geometry_img_8.png)


위의 상황에서 $tr_B.y-bl_A.y < 0$ 이므로 겹치는 영역의 넓이는 음수가 된다. 이는 구해진 $length$가 실제로는 불가능한 음수길이가 나온 것이다. 즉, $length$나 $width$중 음수 길이가 구해진다면 이는 겹치는 부분이 없는 것이다. 
만약 둘 중 하나가 0값을 갖는다면 이는 두 직사각형들이 어떠한 점이나 면에서 겹치지는 않고 접하는 것을 의미한다. 

![rectangle_geometry_img_8](/assets/image/algorithm_ps/rectangle_geometry/rectangle_geometry_img_9.png)

위의 그림처럼 이해해도 좋다. 최초에 직사각형 A와 B가 겹치는 부분이 살구색 직사각형일때 $length > 0$이다. 하지만 직사각형 B를 A와 겹치지 않도록 초록색 화살표 방향으로 슬라이딩 시켰을때 $length < 0$이 된다.

```cpp

int OverlappingArea(const Rectangle& A, const Rectangle& B) {
  Point tr_c{std::min(A.tr_.x, B.tr_.x), std::min(A.tr_.y, B.tr_.y)};
  Point bl_c{std::max(A.bl_.x, B.bl_.x), std::max(A.bl_.y, B.bl_.y)};

  int length = tr_c.y - bl_c.y;
  int width = tr_c.x - bl_c.x;

  if (length < 0 || width < 0) {
    return 0;
  }
  return length * width; // if either length or width is 0, return 0
}

```

### Reference
[1] https://usaco.guide/bronze/rect-geo?lang=cpp