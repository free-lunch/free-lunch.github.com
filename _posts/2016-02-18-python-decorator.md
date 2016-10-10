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


## Simple Usage

~~~ python
def verbose(func):
    def new_function():
        print "Begin", func.__name__
        func()
        print "End", func.__name__
    return new_function

@verbose
def my_function():
    print "hello, world."
~~~

간단히 사용예제를 살펴보도록 하자. Function 호출 전와 후에 시작과 끝을 해당 Function의 이름과 함께 출력하도록 하였다.

~~~
>> my_function()
Begin my_function
hello, world.
End my_function
~~~
자 이제 my_function을 호출해보자. "hello, world"의 출력 전후로 verbose에서 작성한 Begin와 End가 찍히는 것을 볼 수 있다.
이제부터는 함수 위에 @verbose라는 keyword만 달아주면, 편하게 print를 찍을 수 있다!

## Parameter
위에서 wrapping한 함수는 parameter가 없었지만, my_function이 parameter를 가지고 있다면 어떻할 것인가?
그럴땐 아래처럼 verbose에서 new_function을 정의할 때 동일하게 parameter를 정의해주면 된다.

~~~ python
def verbose(func):
    def new_function(name):
        print "Begin", func.__name__
        func(name)
        print "End", func.__name__
    return new_function

@verbose
def my_function(name):
    print "hello,", name
~~~

하지만 이러한 방식은 parameter가 하나인 function에 대해서만 적용이 된다.
만약 아래와 같이 parameter의 수가 각기 다른 function들에 대해서 지원을 해주려면 어떻해야 하는가?

~~~ python
@verbose
def my_function(name):
    print "hello,", name

@verbose
def my_function2(name, age):
    print "My name is ", name
    print "I'm %d years old" % (age)
~~~

이럴땐, *args, **kwargs을 사용하면 된다.

~~~ python
def verbose(func):
    def new_func(*args, **kwargs):
        print "Begin", func.__name__
        func(*args, **kwargs)
        print "End", func.__name__
    return new_func
~~~

## Class Decorator
Decorator를 Class 형태로도 사용할 수 있다.

~~~ python
class verbose:
    def __init__(self, f):
        print "Initializing Verbose."
        self.func = f;

    def __call__(self):
        print "Begin", self.func.__name__
        self.func();
        print "End", self.func.__name__

@verbose
def my_function():
    print "hello, world."

print "Program start"
my_function();
~~~

출력

~~~
Initializing Verbose
Program start
Begin my_func
hello, world
End my_func
~~~

위는 decorator를 class로 만든 예제이다.
출력된 순서를 보면 @verbose 부분에서 class initialize가 된 것을 볼 수 있다.
__call__을 써서 function처럼 callable object로 만들어서 사용한 것을 제외하면,
decorator를 function으로 만든 것과 비슷하다.

참고적으로 Class안에서 Class Decorator를 사용하게 된다면, self를 넘기는 문제때문에 충돌이 날 수 있다.
그럴 땐 결국 해당 Class안에서든 밖에서든 function decorator를 사용해서 해결해야 한다.
자세한 내용은 아래 블로그를 참고하시길

<http://egloos.zum.com/mcchae/v/11030528>

## Example
여기서는 Python PERP에서 소개한 다양한 예제에 대해 적을려고 한다.
가장 많이 사용하는 예는 @classmethod와 @staticmethod라고 하는데,
아래와 같이도 사용할 수 있기에 참고사항으로 적어놓는다.
좀 더 많은 예제는 python wiki에 잘 정리되어 있다.

<https://wiki.python.org/moin/PythonDecoratorLibrary>

### Main 함수가 끝날때 실행되는 함수정의

~~~ python
def onexit(f):
    import atexit
    atexit.register(f)
    return f

@onexit
def func():
    ...

~~~~

### Singleton Class 정의

~~~ python
def singleton(cls):
    instances = {}
    def getinstance():
        if cls not in instances:
            instances[cls] = cls()
        return instances[cls]
    return getinstance

@singleton
class MyClass:
    ...

~~~~

### function에 attributes 추가

~~~ python
def attrs(**kwds):
    def decorate(f):
        for k in kwds:
            setattr(f, k, kwds[k])
        return f
    return decorate

@attrs(versionadded="2.2",
       author="Guido van Rossum")
def mymethod(f):
    ...
~~~~


### Arguemnt와 return type에 대한 체크

~~~ python
def accepts(*types):
    def check_accepts(f):
        assert len(types) == f.func_code.co_argcount
        def new_f(*args, **kwds):
            for (a, t) in zip(args, types):
                assert isinstance(a, t), \
                       "arg %r does not match %s" % (a,t)
            return f(*args, **kwds)
        new_f.func_name = f.func_name
        return new_f
    return check_accepts

def returns(rtype):
    def check_returns(f):
        def new_f(*args, **kwds):
            result = f(*args, **kwds)
            assert isinstance(result, rtype), \
                   "return value %r does not match %s" % (result,rtype)
            return result
        new_f.func_name = f.func_name
        return new_f
    return check_returns

@accepts(int, (int,float))
@returns((int,float))
def func(arg1, arg2):
    return arg1 * arg2
~~~~

### Interface

~~~ python
def provides(*interfaces):
     """
     An actual, working, implementation of provides for
     the current implementation of PyProtocols.  Not
     particularly important for the PEP text.
     """
     def provides(typ):
         declareImplementation(typ, instancesProvide=interfaces)
         return typ
     return provides

class IBar(Interface):
     """Declare something about IBar here"""

@provides(IBar)
class Foo(object):
        """Implement something here..."""
~~~~

### HTML tag 추가

~~~ python
def p_decorate(func):
   def func_wrapper(name):
       return "<p>{0}</p>".format(func(name))
   return func_wrapper

@p_decorate
def get_text(name):
   return "lorem ipsum, {0} dolor sit amet".format(name)

print get_text("John")
~~~~

### Estimate time of fucntion execution

~~~ python
import time
def decorate(func):
    def func_wrapper():
        print "welcome to"
        start = time.time()
        func()
        end = time.time()
        print "with python"
        print "Elapsed time: %5f" % (end-start)
    return func_wrapper

@decorate
def function():
    print "hello world"
~~~

---
## Reference

[Decorators I: Introduction to Python Decorators by Bruce Eckel](http://www.artima.com/weblogs/viewpost.jsp?thread=240808)

<http://trowind.tistory.com/72#footnote_link_72_2>

<http://hackersstudy.tistory.com/51>
