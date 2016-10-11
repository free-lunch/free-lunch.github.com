---
layout: post
comments: true
title: 파이썬 프로파일링을 해보자
tags: [python, profiler]
date: 2016-07-03T21:20:42+09:00
---
요즘 파이썬으로 알고리즘을 풀고 있는데, 안 풀리는 문제들이 허다하다.. -_-;
똑같은 알고리즘으로도 안 풀리는 성능상 문제들이 있다.
그나마 algospot에서는 pypy를 지원해줘서 조~금 더 풀 수 있는 문제가 늘었지만,
여전히 pypy에서도 안 풀리는 문제가 많다.
그러다가 내가 못 푼 문제를 pypy로 푼 분들도 있는데, 이럴 때면 오기가 발동하곤 한다.(하지만 GG..)
내 코드가 어느 부분이 느린지, 확인을 하고 튜닝을 하는 것이다.
이때 어느 부분이 성능상 느린지 확인할 때 사용하는 것이 프로파일링이다.

## cProfile
일단 python에 기본 내장되어 있는 cProfile을 사용해보자.
사용밥은 간단하다.

~~~ python
import cProfile

def test():
    for i in range(5):
        print(i)

cProfile.run('test()')
~~~

위 코드와 같이 run에 체크를 원하는 함수를 스트링으로 넣으면 된다.
이마저 귀찮다 한다면 아래와 작성한 코드를 console로 실행하면 된다.

~~~
python -m cProfile test.py
~~~

실행한 결과는 아래와 같다.

~~~
3 function calls in 0.000 seconds

Ordered by: standard name

ncalls  tottime  percall  cumtime  percall filename:lineno(function)
1    0.000    0.000    0.000    0.000 <stdin>:1(test)
1    0.000    0.000    0.000    0.000 <string>:1(<module>)
1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
~~~

* ncalls : 실행횟수
* tottime : sub function의 실행시간이 제외한 전체 실행시간
* percall : sub function을 제외한 1회 실행시간
* cumtime : sub function을 포함한 전체 실행시간
* percall : sub function을 포함한 1회 실행시간

좀 더 자세한 내용이 알고 싶다면 공식 홈페이지 고고
[https://docs.python.org/2/library/profile.html]


## line_profile
근데 알고리즘을 튜닝하다보면, function 단위보다 라인단위로 보고 싶을 때가 많다.
그래서 찾아보니 line_profile이라는 적합한 것이 있었다.
설치부터 사용법까지 간단하게 살펴보겠다.

1. 설치
- sudo pip install line_profile

2. 사용법
- profile을 원하는 function위에 @profile 로 데코레이터
- kernprof -l 파일명 --> 파일명.lprof 파일 생성
- python -m line_profile 파일명.lprof

자 아마 아래와 같이, 라인별로 프로파일링이 되어서 잘 나올 것이다.
만약 안된다면 아래 공식홈페이지 참고하세요~
[https://github.com/rkern/line_profiler]
여기까지도 훌륭하다.
하지만 내가 궁극적으로 원하는 것은 virtualenv에 알고스팟과 같은 pypy 2.2.1을 깔고,
line_profile이 pypy를 사용하도록 하는 것이다.
하지만 line_profile은 항상 global하게 깔린 python을 바라보도록 되어있다..오마이갓..


~~~
Pystone(1.1) time for 50000 passes = 2.48
This machine benchmarks at 20161.3 pystones/second
Wrote profile results to pystone.py.lprof
Timer unit: 1e-06 s

File: pystone.py
Function: Proc2 at line 149
Total time: 0.606656 s

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
   149                                           @profile
   150                                           def Proc2(IntParIO):
   151     50000        82003      1.6     13.5      IntLoc = IntParIO + 10
   152     50000        63162      1.3     10.4      while 1:
   153     50000        69065      1.4     11.4          if Char1Glob == 'A':
   154     50000        66354      1.3     10.9              IntLoc = IntLoc - 1
   155     50000        67263      1.3     11.1              IntParIO = IntLoc - IntGlob
   156     50000        65494      1.3     10.8              EnumLoc = Ident1
   157     50000        68001      1.4     11.2          if EnumLoc == Ident1:
   158     50000        63739      1.3     10.5              break
   159     50000        61575      1.2     10.1      return IntParIO
~~~

## pprofile
line_profile로 좌절하고 있을 때쯤, 한줄기 희망이 보였다.
비슷하게 virtualenv에서 line_profile을 쓰기 힘들다고 만들어놓은 라이브러리가 있었다.(얏호)

공식 사이트 : <https://pypi.python.org/pypi/pprofile/1.6.2>

1. 설치
- virtualenv -p pypy경로 venv (pypy 설치를 위해서 -p 옵션)
- cd venv
- source bin/activate
- pip install pprofile

2. 사용법
- pprofile 파일명

thread도 프로파일링이 가능한 것 같고, import된 라이브러리까지 분석해준다.
또한 cProfile처럼 함수만 프로파일링이 가능하다.
알고리즘을 분석하기 위해서 나한테 적합한 것 같다.


이만 끝~
