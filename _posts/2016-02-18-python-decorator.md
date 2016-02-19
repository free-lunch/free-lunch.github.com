---
layout: post
comments: true
title: Decorator
modified:
categories: Python
excerpt:
tags: [python, decorator]
image:
  feature: python.png
date: 2016-02-18T16:00:47+09:00
---

{% include _toc.html %}
---

## Decorator?
Decorator는 functions, methods, classes 같은 callable object들을 수정하지 않고 다른 행동을 할 수 있도록 할 수 있다. 일단 Syntax를 살펴보도록 하자.

## Syntax

~~~ python
1.
@function_wrapper
def function():
    pass 
------------------------------------
2.
def function():
    pass

function = function_wrapper(function)
~~~
1번과 2번은 완전히 동일하다. 위와 같은 상황에서 function을 호출하게 되면, 실제적으로는 function을 인자로 가진 function_wrapper가 호출되게 된다. 즉 function을 function_wrapper로 한단계 장식? 한셈이 되는 것이다.
이러한 일을 @ symbol을 통해서 편하게 사용 가능하며, [Sugar syntax](https://en.wikipedia.org/wiki/Syntactic_sugar)이라 할 수 있다.

