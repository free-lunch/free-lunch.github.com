---
layout: post
comments: true
title: 서브라임 세팅
categories: Sublime Text
tags: [sublime, sublimetext3, configuration, tool]
date: 2016-01-05T06:19:50+00:00
---


## Installation Package Control

1. Press ctrl+`
2. Type a code in link (<https://packagecontrol.io/installation>)

---

## Trailing space remove
1. Preferences -> Settings - User에 아래 항목 추가


"trim_trailing_white_space_on_save": true


2. install package -> trailing space 설치, 자세한 사용방법은 아래에
  https://github.com/SublimeText/TrailingSpaces

> PS. 개인적으로는 둘 다 설치하면 좋은 것 같다.
      trailing space는 trailing space를 시각적으로 보여주는 기능이 있고 단축키 지정으로 중간중간 정리할 수 있으며,
      settings 값 조절로는 저장 할때 마다 정리를 해줘서 둘다 설정하면 이중 정리가 가능하다.
      번외로.. vim에서는 %s/\s\+$//로도 정리가 가능하다.

---

## Changing between space to tab
1. 우측 하단 메뉴의 space를 클릭한뒤, Convert indentation~을 선택
2. Preferences -> Settings -User에 아래 항목을 추가
"translate_tabs_to_spaces": true,

<https://css-tricks.com/changing-spaces-tabs-sublime-text/>


---

## Install Predawn theme
1. Ctrl + Shift + P
2. Type Package Control : Install Package
3. Search predawn
4. Preferences -> Settings - User 에서 아래 세팅 추가

{% highlight cpp %}
{
  "theme": "predawn.sublime-theme",
  "color_scheme": "Packages/Predawn/predawn.tmTheme"
}
{% endhighlight %}

Reference : [https://github.com/jamiewilson/predawn]
