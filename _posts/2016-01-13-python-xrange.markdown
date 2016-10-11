---
layout: post
comments: true
title: xrange는 무엇인가? (python 2 기준)
categories: Python
tags: [python, xrange]
date: 2016-01-13T12:58:57+00:00
---

Python에서 range와 xrange의 차이를 남기기위해 포스트를 작성하였다.
앞으로 python을 배우면서 알게된 문법들을 하나씩 남기도록 하려고 한다.

##  range() vs xrange()
일단 range()의 사용법에 대해 먼저 알아보자.
{% highlight python %}
for i in range(1, 11):
	sum = sum + i

print(sum) # 55
{% endhighlight %}

range(1,11)은 1부터 10까지의 숫자를 데이터로 갖는 리스트를 리턴한다.
보통 for를 이용한 반복문을 작성할 때에는 위와 같이 range() 함수를 주로 이용하게 된다.

그러면 xrange()의 사용법을 알아보자.
{% highlight python %}
for i in xrange(1, 11):
	sum = sum + i

print(sum) # 55
{% endhighlight %}
차이점을 알겠는가? 그렇다.. 동일하다 -_-;;
사용법은 동일하다. 하지만 내부적으로는 동일하지 않다.
range는 사용을 하든 하지 않든 1부터 10까지의 전체 list를 만든다.
하지만 xrange는 rangeobject라는 고정적인 object를 리턴한다.

rangeobject를 살펴보자.
{% highlight cpp %}
typedef struct {
    PyObject_HEAD
    long        start;
    long        step;
    long        len;
} rangeobject;
{% endhighlight %}

xrange는 오로지 위의 내용만을 가지고 필요할 때만 값을 만들어 낸다. (만들어내는 것은 iterator가 대신한다.)
즉, range는 처음부터 끝까지 모든 값을 만들어 낸 뒤 사용하고,
xrange는 고정된 사이즈의 object만을 이용하여 필요할 때만 값을 생성한다.

그래서 눈에 보이는 가장 큰 장점은 메모리 효율성이다. 아래는 메모리 사용량을 비교한 링크이다.
<http://www.linuxnix.com/python-range-vs-xrange-functions/>

또한 전체 리스트를 만들어내는 시간으로 인해 그런지 속도도 약간 더 빠르다.
{% highlight cpp %}
> python -m timeit "for i in range(1000000):" " pass"
10 loops, best of 3: 26.8 msec per loop
> python -m timeit "for i in xrange(1000000):" " pass"
10 loops, best of 3: 14.4 msec per loop
{% endhighlight %}
위는 range와 xrange로 실행한 속도를 비교한 것이다.
xrange는 range에 비해 47%가 소요시간이 감소하였다.

##  python 3에서는?
xrange가 삭제되고, range가 xrange처럼 동작한다
다만, python3의 range와 python2의 xrange를 비교한다면 xrange가 다소 앞선다.
<http://stackoverflow.com/questions/15014310/why-is-there-no-xrange-function-in-python3>
