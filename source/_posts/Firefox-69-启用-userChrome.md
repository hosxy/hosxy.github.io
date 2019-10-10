---
title: Firefox 69 启用 userChrome.css
date: 2019-10-09 20:23:56
tags: [Firefox]
---





众所周知 Mozilla 正在一步步淘汰 XUL，前不是很久 Firefox 已经淘汰了老 XUL 扩展，全面拥抱 google chrome主导的 webextension。

现在 Firefox 69 版本起默认不加载`userChrome.css`了，当然这是对新用户的，如果你在老版本的 Firefox 用了 `userChrome.css` ，然后升级到 69 是不会禁用的。

如果是新装的怎么办呢？也很简单，打开`about:config`，搜索`toolkit.legacyUserProfileCustomizations.stylesheets`，将其置为 `true`，然后重启浏览器即可。

这里顺便贴下我的 `userChrome.css`：

```css
/*57+去除左上角空白*/
.titlebar-spacer[type="pre-tabs"] {
	display: none !important;
}

/*去除无用书签右键菜单*/
#placesContext_open,
[command="placesCmd_open:tab"], 
[class="openintabs-menuitem"],/*在标签页中打开书签组*/
[id="placesContext_open:newprivatewindow"],/*在新隐私窗口中打开*/
[id="placesContext_openContainer:tabs"]/*在标签页中打开书签组*/ {
	display: none !important;
}

/*去除右键前进、后退、刷新、书签菜单*/
#context-navigation{
	display: none !important;
}

/*去除“发送到设备”右键*/
#context-sendpagetodevice, #context-sep-sendpagetodevice,
#context-sendlinktodevice, #context-sep-sendlinktodevice,
#context_sendTabToDevice, #context_sendTabToDevice_separator {
  display: none !important;
}

/*去除右键搜索*/
#context-searchselect{
	display: none !important;
}

/*去除无用图像右键菜单*/
#context-setDesktopBackground,#context-sendimage,#context-viewimageinfo {
	display: none !important;
}

/*隐藏所有右键菜单分割线*/
menuseparator {
  display: none !important;
}

/*去除 链接存为书签*/
#context-bookmarklink{
	display: none !important;
}

/*去除 为搜索引擎添加关键词*/
#context-keywordfield{
	display: none !important;
}

/*去除拼写检查()*/
#spell-check-enabled {
	display: none !important;
}

/*设置地址栏圆角*/
#urlbar {
	border-radius: 2em !important;
}

/*地址栏收藏按钮向左偏移 6px*/
#star-button {
    margin-right: 6px !important;
}

```





Firefox