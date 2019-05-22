---
title: 用cmd批处理实现类似linux软链接功能
date: 2019-05-22
tags: [Windows,MSYS2,cmd]
---

###  1、问题由来

最近在玩 `MSYS2` ，但是由于`MSYS2`的目录下东西太多，不想将其加入到环境变量`PATH`中，以免发生冲突，出现莫名其妙的问题。但是`windows`下没有类似`linux`下的软链接功能，总不能每次执行命令时都带上路径吧，这绝对不能接受。然后突然就想到了可以写个`cmd`批处理啊 ヾ(≧▽≦*)o 。

###  2、解决方法

然后就写了个最简单的批处理：

``` cmd
set MSYS2_HOME=D:\MSYS2
set Path=%Path%;%MSYS2_HOME%\usr\bin

set COMMAND=%MSYS2_HOME%\usr\bin\git.exe
%COMMAND% %1 %2 %3 %4 %5 %6 %7 %8 %9
```

然后就用起来了，刚开始用着也没什么问题，然后用着用着突然发现要是传递的参数超过9个怎么办 w(ﾟДﾟ)w。然后就去网上搜了下，然后就把批处理改进了下，然后它就可以支持超过9个参数了 (≧∇≦)ﾉ 。实现如下：

``` cmd
@echo off   

set MSYS2_HOME=D:\MSYS2
set Path=%Path%;%MSYS2_HOME%\usr\bin

set COMMAND=%MSYS2_HOME%\usr\bin\git.exe

set ARGV=
set str="%1"

:param
if "%str%"=="" (
    goto end
)
set ARGV=%ARGV% %str%
shift /0
set str="%1"
goto param

:end
%COMMAND% %ARGV%
```

###  3、意外之喜

然后我想`MSYS2` 的终端里执行`git`是有本地化的(中文)，`cmd`里是不是也可以。然后我就尝试着在`cmd`里面执行命令前先设置了一个环境变量：`set LANG=zh_CN.UTF-8 `  然后就和你们想的一样，输出是带有中文的 (/≧▽≦)/ 。当然这一切的前提是在windows的区域设置里面开启了UTF-8，否则肯定乱码。

为了方便不能每次都手动设环境变量吧，直接把 `set LANG=zh_CN.UTF-8`放到批处理里岂不妙哉，或者直接在 windows 环境变量里，设置变量名为 `LANG`，值为`zh_CN.UTF-8`，这样就不用为每个批处理都加上`set LANG=zh_CN.UTF-8`了，然后成品就是这样：

``` cmd
@echo off   

set LANG=zh_CN.UTF-8
set MSYS2_HOME=D:\MSYS2
set Path=%Path%;%MSYS2_HOME%\usr\bin

set COMMAND=%MSYS2_HOME%\usr\bin\git.exe

set ARGV=
set str="%1"

:param
if "%str%"=="" (
    goto end
)
set ARGV=%ARGV% %str%
shift /0
set str="%1"
goto param

:end
%COMMAND% %ARGV%
```



