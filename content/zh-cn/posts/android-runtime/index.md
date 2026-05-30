---
title: "Android Runtime"
date: 2016-12-07
categories:
  - "engineering"
tags:
  - "android"
---

这张图来自维基百科的[Android Runtime](https://en.wikipedia.org/wiki/Android_Runtime)词条，图中画出了Dalvik和ART架构的对比。差别在于安装APK时，Dalvik架构用dexopt对dex的文件进行了优化，但仍旧是Java的字节码；而ART架构用dex2oat工具将dex的文件转换为ELF格式的可执行文件。众所周知，ELF格式是Linux上通用的可执行文件格式，由操作系统加载，硬件处理器直接执行。这也使得使用ART架构的Android版本能够取得运行时的性能提升。Android是从4.4 KitKat中开始引入这个技术，在5.0 Lollipop中正式替换的。

[![](https://upload.wikimedia.org/wikipedia/commons/thumb/2/25/ART_view.png/1315px-ART_view.png)](https://upload.wikimedia.org/wikipedia/commons/thumb/2/25/ART_view.png/1315px-ART_view.png)
