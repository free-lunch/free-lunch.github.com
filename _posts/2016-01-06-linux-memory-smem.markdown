---
layout: post
title: Linux Memory - Smem
modified:
categories: 
description:
tags: [linux, memory, smem, smemcap]
image:
  feature: Smem-viewer.jpg
  credit:
  creditlink:
comments:
share:
date: 2016-01-06T02:01:19+00:00
---
---

## 1. 목적

이 글을 쓰게된 이유는 리눅스에서 프로세스별 메모리 사용량을 파악하기 위해서이다.

---

## 2. 용어
프로세스의 메모리를 완벽하게 측정하는 방법은 없다.
 측정하는 여러 방식 중에서 가장 적합하다고 생각되는 것을 선택을 해야한다. 
그러기 위해서는 아래와 같은 방식들을 이해를 해야한다. 

* VSS : 프로세스와 관계되는 모든 Virtual 메모리의 크기
* RSS : 프로세스와 관계되는 물리적 페이지의 수
(Shared memory를 중복 포함하여 계산되서 실제보다 크게 측정
* USS : shared memroy를 불포함
* PSS : Shared memory를 사용하는 프로세스의 수로 shared memory를 나누어서 포함

참고 : <http://ecogeo.tistory.com/255>

기본적으로 free나 top을 통해 나오는 메모리 값은 RSS나 VSS이다. 
즉, 다시말해 free나 top을 통해 나오는 메모리는 실제 사용메모리보다 크게 나온다는 얘기이다.
정확한 데이터를 위해서는 USS나 PSS를 사용해야 한다. 
여기서는 PSS를 선택하여 계산하도록 한다.

---

## 3. Tool
우리가 필요한 정보들은 아래에 모두 존재한다.

/proc/$pid/cmdline

/proc/$pid/smaps

/proc/$pid/stat

/proc/meminfo

/proc/version

하지만 모든 프로세스를 일일히 볼수는 없다. smem이라는 툴을 사용하도록 하자.

Ubuntu 기준으로 apt-get install smem으로 설치가 가능하다.
smem은 현재 기준으로 결과를 보여준다.
특정 시점을 캡쳐하고 싶으면, smemcap을 이용하도록 하자.
smemcap은 /proc/의 data를 tar로 묶어준다.
설치는 위와 동일하게 apt-get install smemcap으로 가능하다.
소스코드가 필요하다면 아래 site로 go
<https://www.selenic.com/smem/>


###Usage

|Show basic process information|smem|
|Show library-oriented view|smem -m|
|Show user-oriented view|smem -u|
|Show system view|smem -R 4G -K /path/to/vmlinux -w|
|Show totals and percentages|smem -t -p|
|Show different columns|smem -c "name user pss"|
|Sort by reverse RSS|smem -s rss -r|
|Show processes filtered by mapping|smem -M libxml|
|Show mappings filtered by process|smem -m -P [e]volution|
|Read data from capture tarball|smem --source capture.tar.gz|
|Show a bar chart labeled by pid|smem --bar pid -c "pss uss"|
|Show a pie chart of RSS labeled by name|smem --pie name -s rss|

