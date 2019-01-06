---
title: Weasel输入法(Rime)生僻字过滤
date: 2019-01-06 19:00:42
tags: [Weasel,Rime]
---

Rime输入法是一款优秀的输入法，支持windows，Linux，OSX。其在 windows 下的实现叫做 Weasel输入法。Rime输入法以其可定制性极强而闻名。
weasel输入法默认的字符集太大，里面有许多生僻字，一般情况下用不上。如何如何下面主要说一下如何过滤掉这些生僻字。

## 修改配置文件
首先在Weasel输入法的用户文件夹里新建/修改文件 `luna_pinyin_simp.custom.yaml`,然后写入以下内容：
``` yaml
patch:
   
  switches:
    - name: ascii_mode
      reset: 0
      states: [ 中文, 西文 ]
    - name: full_shape
      states: [ 半角, 全角 ]
    - name: zh_simp                                     # 简繁转换
      reset: 1
      states: [ 漢字, 汉字 ]
    - name: ascii_punct
      states: [ 。，, ．， ]
    - options: ["utf8", "gbk", "gb2312"]                # 字符集选单
      reset: 0                                          # 默认 UTF-8
      states:
        - UTF-8
        - GBK
        - GB2312
        
  engine/filters:
    - simplifier
    - uniquifier
    - charset_filter@gbk                                # GBK过滤
```
然后重新部署输入法。

## 设置
按 ` Ctrl+`  键打开Weasel设置，选择 `朙月拼音•简化字` 之后选择 `GBK`，然后在打字是不是发现那些生僻字已经没有了。