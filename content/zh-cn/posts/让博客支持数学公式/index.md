---
title: "让博客支持数学公式"
date: 2015-02-05
categories: 
  - "个人博客"
---

之前的解决方案通过JavaScript脚本将公式转换为图片，即使用[ASCIIMathTeXImg.js](http://mjm1990.com/wp-content/uploads/2015/02/ASCIIMathTeXImg.js)，在<head></head>中引用如下的代码。

```
<script type="text/javascript" src="<?php bloginfo('template_url'); ?>/js/ASCIIMathTeXImg.js">
```

后面Google时发现了MathJax，提供了更加强大的公式服务。直接引用下面的代码即可支持。

```
<script type="text/javascript"
```

相比于第一种方式，第二种方式优势至少有两点：其一，加载的JavaScript脚本都是引用别人的链接，不会耗费自己托管服务器的流量；其二，功能更多，支持用户自定义，可以选择LaTex、MathML或者AsciiMath，解析结果也不再是图片，而是HTML、SVG或MathML。

直接用[MathJax](http://www.mathjax.org/)服务唯一的担心是，如果被墙将无法访问，像Google的字体服务一样。实际上，MathJax也支持将脚本[安装到自己的博客服务器](http://docs.mathjax.org/en/latest/start.html)上，源代码托管在[GitHub](https://github.com/mathjax/MathJax)上，但整个程序脚本较多，占空间，也会耗流量，所以还是推荐在没有被墙的情况下，直接使用服务为好。

下面这个公式用第二种方式生成。

$$e^{i\\pi}+1=0$$
