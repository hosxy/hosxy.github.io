---
title: Debian下chromium开启flashplayer及扩展支持
date: 2017-03-26 18:09:32
tags: [chromium,flash,debian,linux]
---

### chromium开启扩展支持
最近在debian上使用chromiun时发现chromium不能装扩展，这怎么能行，最后折腾了一番，才发现是debian源里的chromium默认没有开启扩展支持，需手动开启，方法如下：

添加/etc/chromium.d/extensions-flags文件并增加以下内容：  

    #Enable support extensions
    CHROMIUM_FLAGS="$CHROMIUM_FLAGS --enable-remote-extensions"  

### chromium手动开启flash player支持
chromium不同于chrome，本身并没有没有自带flash player，很多发行版官方源就有相应软件包，这里教大家手动开启chromium的flash player支持：   
在/etc/chromium.d/ 下新建PepperFlash-flags文件并添加以下内容  

    #Enable PepperFlash
    
    flashso="$HOME/.config/chromium/PepperFlash/libpepflashplayer.so"

    flashversion=`strings $flashso 2> /dev/null | grep LNX | cut -d ' ' -f 2 | sed -e "s/,/./g"`

    CHROMIUM_FLAGS="$CHROMIUM_FLAGS --ppapi-flash-path=$flashso --ppapi-flash-version=$flashversion"

注：flashso代表的是你的libpepflashplayer.so的位置，没必要和我一样,我这样写是保持和chrome自带的flash路径相似，还有就是如果你电脑有多个用户，就不能用$HOME变量，而要用绝对路径。

最后补充一点，有时候明明加载了flash插件，但是看视
频的时候还是提示没装flash插件，这时候你可以在网址输入栏输入：chrome://flags  找到  首选 HTML（而非 Flash），然后选择：已停用