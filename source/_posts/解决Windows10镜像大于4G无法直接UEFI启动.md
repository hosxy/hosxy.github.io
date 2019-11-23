---
title: 解决 Windows10 镜像大于 4G 无法直接 UEFI 启动
date: 2019-11-23 21:58:45
tags: [Windows,UEFI]
---

# 问题

很多人应该都知道，如果主板使用 UEFI 启动的话，可以直接将 Windows10 的安装镜像解压到U盘(文件系统为 FAT32)根目录，然后启动项选择U盘就可以正常启动 Windows10 安装程序。但是 FAT32 文件系统历史有点久远了，无法存放大于4G的单文件，但是随着 Windows 不断更新，安装镜像也越来越大，之前 Windows10的安装镜像就已经超过了 4G，但所幸 ISO 里面的单文件没有哪个超过 4G，但是今天下载 Windows10 1909的 ISO 时发现里面的 `install.wim` 已经正式超过 4G，达到了将近 4.2G，所以就无法将 ISO 直接解压到文件系统为 FAT32 的 U 盘里了。

# 解决方法

###  直接将U盘格式化为 NTFS 文件系统

可以直接将U盘格式化为 NTFS 文件系统，NTFS 没有单文件4G的限制。但是不推荐这种做法，首先这不是通用的方法，不是所有的主板都认 NTFS 文件系统的 ESP 分区的，其次 NTFS 作为一个日志式文件系统，对U盘伤害比较大，并不适合。当然这也是一个办法，虽然不怎么好。

###  分割镜像

既然存在这种问题，微软当然也是考虑过的。解决方法就是将超过 4G 的单文件分割成多个小于4G的文件。具体做法如下：

+   首先以管理员身份打开一个 cmd 也就是命令提示符。

+   使用以下命令分割 `install.wim` 文件:

    ```
    dism /Split-Image /ImageFile:G:\sources\install.wim /SWMFile:E:\install.swm /FileSize:4000
    ```

    `/ImageFile`指定`install.wim`文件的位置，`/SWMFile`指定分割后输出文件的名称及位置，`/FileSize`指定分割后的单文件最大大小，单位是MB。<br>如上命令最后会在 E 盘输出两个文件分别为 `inatall.swm`和``install2.swm`
    
+  将ISO里面除`install.wim`外的文件及目录复制到U盘根目录，然后将`install.swm`和`install2.swm`复制到之前`install.wim`所在的目录即可(其实就是用`install.swm`和`install2.swm`替代`install.wim` )

+  然后正常使用 UEFI 启动即可。

我个人推荐这种做法，虽然略有一点麻烦。