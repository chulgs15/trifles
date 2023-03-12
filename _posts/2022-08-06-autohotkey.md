---
published: true
layout: single
title: "[Windows] AutoHotkey"
comments: true
categories:
  - os
tags:
  - [windows]
date: 2022-08-06
last_modified_at: 2022-08-06
toc: true
toc_sticky: true
---
평소 사용하는 autohotkey 를 정리.

![슬라이드1](https://user-images.githubusercontent.com/22446581/183247400-c5c8cf95-8369-4b10-9523-80e4c1a2ab44.PNG)

# Version

* OS : Windows 10

# 결론

* 평소 사용하는 AutoHotkey 설정을 정리.

# 설정

```
LShift & Space::
    ;; 한영 변환을 LShift + Space 로 변환.
    Send, {vk15sc138} 
Return

LWin & Enter::
    ;; Alt + Tab =>  Ctrl + Alt + Tab
    Send, {LAlt down}{LCtrl down}{Tab down}{Tab up}{LCtrl up}{LAlt up} ;; 
Return

RShift & a::
    ;; Alt + Tab =>  Ctrl + Alt + Tab
    Send, *
Return

RShift & s::
    Send, _
Return

RShift & d::
    Send {Backspace}
Return

RShift & e::
    Send % "="
Return

RShift & r::
    Send % "%"
Return

RShift & z::
    Send % "'"
Return

RShift & w::
    Send % "select *"
    Send, {Enter}
    send % "from   "
    Send, {Enter}
    send % "where  1=1"
    Send, {Enter}
    send % ";"
    Send, {Up}{Up}
    Send, {End}
Return
```