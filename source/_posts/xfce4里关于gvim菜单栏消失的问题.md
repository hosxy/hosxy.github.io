---
title: xfce4里关于gvim菜单栏消失的问题
date: 2017-03-26 18:19:39
tags: [gvim,debian,linux]
---

今天在我的debian xfce4装gvim时，发现gvim的菜单栏消失，没有字，但是能点出来，所以就到网上去搜了一下，确认是编码的问题

终端输入locale 得到系统编码是 zh_CN.utf8

    ls /usr/share/vim/vim80/lang  
发现gvim的编码是menu_zh_cn.utf-8.vim

所以建个软链：

    cd /usr/share/vim/vim80/lang

    sudo ln -s menu_zh_cn.utf-8.vim menu_zh_cn.utf8.vim

然后重启gvim菜单栏就能正常显示了