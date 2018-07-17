---
title: linux下firefox安装flash插件
date: 2017-03-26 20:36:42
tags: [firefox,flash,linux]
---

firefox安装flash插件比较方便，首先创建目录：
    
    mkdir -p ~/.mozilla/plugins

然后到adobe官网下载适用firefox的npapi版的flash，然后将解压得到的libflashplayer.so放到 ~/.mozilla/plugins 目录下，然后重启firefox。
不过这样安装的flash只有当前用户能用，不是全局的，如果想要全局效果，就要把 libflashplayer.so 放到 /usr/lib/mozilla/plugins 目录下。
