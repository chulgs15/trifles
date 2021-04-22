---
published: true
layout: single
title: "[Windows] alias로 jekyll serve 시작."
comments: true
classes: wide
categories:
  - os
tags:
  - [triles, os]
date: 2021-04-22
last_modified_at: 2021-04-22
---
 Blog 내용을 확인하기 위해 매일 실행하는 반복적인 작업...멈춰~!!

## [실패] alias & pipe

처음 시도한 방법은 alias로 실행 명령어를 주는 방법이었다.

```powershell
PS D:\github\trifles> set-alias trifles cd D:\github\trifles\ | jekyll serve
Set-Alias : 'D:\github\trifles\' 인수를 허용하는 위치 매개 변수를 찾을 수 없습니다.
위치 줄:1 문자:1
+ set-alias trifles cd D:\github\trifles\ | jekyll serve
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (:) [Set-Alias], ParameterBindingExceptio 
   n
    + FullyQualifiedErrorId : PositionalParameterNotFound,Microsoft.PowerShell.Commands. 
   SetAliasCommand
```

하지만 set-alias는 pipe로 연결해서 지정할 수 없었다. 

## start.ps1

내가 원하는 기능을 만들러면 PowerShell script를 만들고 실행 script 명령어를 alias로 주는 방법니다. ';' 이 PowerShell에서 명령어 구분자 역할을 한다.

```powershell
cd D:\github\trifles;
jekyll serve
```

## profile.ps1

 `set-alias`는 실행한 Terminal에서만 적용되서, 재실행하면 alias가 사라진다.  그래서 `Profile.ps1`파일에 내가 설정한 alias를 넣어준다. Terminal이 실행할 때 마다 `Profile.ps1`의 `set-alias`를 설정한다.

 참고로 profile.ps1은 특정 6개의 폴더경로로 적용 범위를 다르게 가져갈 수 있다. [이 곳](https://web.archive.org/web/20121105003839/http://blogs.technet.com/b/heyscriptingguy/archive/2012/05/21/understanding-the-six-powershell-profiles.aspx)을 참조하자.

```powershell
PS C:\Users\사용자 계정\Documents\WindowsPowerShell> cat .\Profile.ps1
set-alias tri D:\github\trifles\start.ps1  
```

## 실행

```powershell
PS C:\Users> tri
Configuration file: D:/github/trifles/_config.yml
            Source: D:/github/trifles
       Destination: D:/github/trifles/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
                    done in 2.164 seconds.
 Auto-regeneration: enabled for 'D:/github/trifles'
    Server address: http://127.0.0.1:4000
  Server running... press ctrl-c to stop.
```

### 실해 시 권한 부족으로 에러가 발생할 경우.

PowerShell을 `관리자 권한`으로 실행해야 한다.

```powershell
PS C:\Users> set-executionpolicy unrestricted
PS C:\Users> executionpolicy -list

        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process       Undefined
  CurrentUser       Undefined
 LocalMachine    Unrestricted
```



## 참고

* https://gocoder.tistory.com/1510
  * 별칭 생성.
* https://stackoverflow.com/questions/1772367/powershell-is-it-possible-to-set-alias-on-a-piped-command
  * pipe 생성 불가 확인.
* https://web.archive.org/web/20121105003839/http://blogs.technet.com/b/heyscriptingguy/archive/2012/05/21/understanding-the-six-powershell-profiles.aspx
  * set-alias 저장.
* https://samsons.tistory.com/16
  * execution policy