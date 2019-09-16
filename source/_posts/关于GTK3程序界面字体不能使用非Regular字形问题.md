---
title: 关于GTK3程序界面字体不能使用非Regular字形的问题
date: 2019-09-16 22:31:45
tags: [GTK3,Font,Linux]
---



# GTK3界面字体的bug

简单来说就是GTK3程序界面不能使用非`Regular`字形(也不一定一定就是`Regular`,应该说是默认字形)。比如说 Linux 桌面里使用最多的简体中文字体之一 `思源黑体`有时候也叫作`Source Han Sans `和`Noto Sans CJK`，其实都是一个字体(它由Google和Adobe联合设计)，但是Google和Adobe对它叫法不一样，它共有7个字形分别是 `ExtraLight`, `Light`, `Normal`,  `Regular`, `Medium`, `Bold`, `Heavy`。对于GTK3程序的界面你无论把字体设置成这七个中的哪一个，最后GTK都只会使用`Regular`字形。这是个bug，而且时间已经很久了，估计也不会修了。但是GTK2却没有这个毛病。

**Tips1：这里特别强调下是GTK3程序界面字体，不包括比如文本编辑器，终端等带文本界面的文本字体**

**Tips2：这也可能不是 bug，是 Feature，毕竟GTK没有 bug，只有 Feature  （︶^︶）。**



# 解决方法

## 1、直接不用gtk3程序

这是最好最直接最有效的方法了，直接换用 QT 写的桌面环境，比如 KDE , LXQT，尽量使用 QT 写的程序，毕竟 QT 可没有这种 BUG，然后从此远离 GTK。

## 2、用 Fontconfig 映射字体

这又回到那个 bug/feature 了，为什么设置其他字形无效呢？就拿思源黑体简体中文来说，你无论设置成什么字形，GTK 最后貌似都会 fallback 到 `思源黑体 CN`这个不带任何字形的字形上，然后不带字形一般都是指向`Regular`。而且貌似这其中还有一些其他的玄学问题。

知道这个原因后我们就可以通过 Fontconfig 的配置来解决问题。我的做法是让 `Sans` 映射到`思源黑体 CN Medium`这个字体上，然后你在把界面字体设置成 `Sans`就可以达到目的了。

下面给一个 Fontconfig 配置文件的参考写法：

``` xml
	<!-- 将 sans-serif 映射到 Source Han Sans CN Medium -->
	<match>
        <test name="family"><string>sans-serif</string></test>
        <edit name="family" mode="prepend" binding="strong"><string>Source Han Sans CN Medium</string></edit>
    </match>
```

**Tips：不要傻乎乎的把字体名直接写 `Sans`，要写成 `sans-serif` ，还有就是字体名最好还是用英文。**

但是这么做的同时也会引入新的问题，那就是没办法用粗体了，毕竟是没有`思源黑体 CN Medium Bold`这个字体的。但是程序界面一般也不会用粗体，所以我个人觉得这点代价还是可以接受的。