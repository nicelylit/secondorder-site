---
title: "CSS实现垂直居中"
date: 2015-11-01
categories:
  - "engineering"
tags:
  - "软件开发"
---

在常规的网页中，宽一般有最大限制，而高没有，因而CSS规范并没有给出块级元素和多行文字垂直居中，建议性的实现方案。换句话说，但凡有垂直居中的要求，必然是设计者对包含块的高做了固定限制。

默认的CSS块级元素的高由内容决定，通常内容有多少，高度就会算多少。固定了高意味着设计者必须确认内容的高度不会超过限制，否则会产生不合预期的结果。

众所周知，限制高仅需要指定height样式属性即可。

确认内容高度需要了解内容的组成，可能会是一张可替换的图片，也可能会是一段行数不确定的文本，甚至也可能是另外一个块级元素，每种内容有各自的模型在控制着自己的展示。

### 情形1: 垂直居中一张图片

图片的模型最简单，行内元素，限制高，宽会等比例缩放。假设要在高度为200px的块中放置一个高度不超过100px的图片，垂直居中可以这样写：

```css
#middleWrapper {
  height: 200px;
  line-height: 200px;
  border-color: black;
  border-width: 1px;
  border-style: solid;
}
#middleWrapper > img {
  vertical-align: middle;
  max-height: 100px;
}
```

```html
<div class="middleWrapper">
  <img src="/wp-content/uploads/2015/01/violin.jpg">
</div>
```

<div style="height: 200px; line-height: 200px; border-color: black; border-width: 1px; border-style: solid">
  <img src="/wp-content/uploads/2015/01/violin.jpg" style="vertical-align: middle; max-height: 100px">
</div>

上面的代码中重要之处在于line-height恰好等于height，这样整个包含块就变成了一行，在单行内居中行内元素，只需要指定vertical-align是middle即可。

垂直居中一张图片的应用场景很多，比如一个带有标志图片的按钮，再比如购物网站商品页上商品的图片展示（固定的方框内放各种不同尺寸的图片）等。

### 情形2: 垂直居中一行

这种情况与情形1类似，不同之处在于文字如果一行放不下会换行，当换出新的一行，文字便超出了包含块，这时通常的处理手法是将超出部分隐藏（overflow: hidden）。更加普遍的做法是设置样式属性white-space为nowrap不允许文字换行，并且将超出部分换用三个点代替（text-overflow: ellipsis）。下面的例子中，将内容超过一行的文本，垂直居中地放在高200px的包含块中，并且只显示一行。

```css
#middleWrapper {
  height: 200px;
  line-height: 200px;
  text-overflow: ellipsis;
  overflow: hidden;
  border-color: black;
  border-width: 1px;
  border-style: solid;
}
#middleWrapper > span {
  vertical-align: middle;
  white-space: nowrap;
}
```

```html
<div class="middleWrapper">
  <span>It is a truth universally acknowledged that a single man in possession of a good   fortune must be in want of a wife. However little known the feelings or views of such a man may be on his first entering a neighbourhood, this truth is so well fixed in the minds of the surrounding families, that he is considered as the rightful property of some one or other of their daughters.</span>
</div>
```

<div style="height: 200px;
  line-height: 200px;
  text-overflow: ellipsis;
  overflow: hidden;
  border-color: black;
  border-width: 1px;
  border-style: solid">
  <span style="vertical-align: middle; white-space: nowrap">It is a truth universally acknowledged that a single man in possession of a good   fortune must be in want of a wife. However little known the feelings or views of such a man may be on his first entering a neighbourhood, this truth is so well fixed in the minds of the surrounding families, that he is considered as the rightful property of some one or other of their daughters.</span>
</div>

注意white-space属性可以放在包含块中或是直接放在文本标签中均可，因为这个属性允许继承，并且只能作用在文本上。两个overflow属性却要放在包含块中，用户代理需要根据包含块的高度进行计算隐藏掉那一部分文字。值得一提的是text-overflow属性是CSS3中加入的新内容，主流浏览器都支持，但也有些例外，比如UC浏览器的Android手机版。

垂直居中一行文字的应用场景包括按钮上的文字，水平导航栏中的文字等等。

### 情形3: 垂直居中几行文字

如果要垂直居中的文字行数是确定的，并且包含块框的高度恰好等于总行框的高度，那么只需要将包含块的高度设置为总行框高度即可。例如有两行，字体13px，每行行高17px，那么包含块的高度为34px。

这种情形最常见的应用场景是移动购物网站，浏览页商品描述的格式。这种场景下往往也要求能够隐藏多出来的行，以及将多出的部分替换为三个点。这种做法，纯粹的CSS无法胜任，只能计算出行宽，估算出展示的字符数用JS脚本控制。说估算是由于英文字体多数不是等宽，如果无法知道每个字母的宽度，难以精确地算出要展示的字符数目。

### 情形4: 垂直居中一段文字

当一段文字行数不确定，且包含块的高度一定大于总行框高度时，需要将整段文字放在一个不限制高的行内块级元素中，像下面的写法一样：

```css
#middleWrapper {
  height: 200px;
  line-height: 200px;
  border-color: black;
  border-width: 1px;
  border-style: solid;
}
#middleWrapper > div {
  vertical-align: middle;
  display: inline-block;
  line-height: 1.5;
  text-align: justify;
}
```

```html
<div class="middleWrapper">
  <div>It is a truth universally acknowledged that a single man in possession of a good fortune must be in want of a wife. However little known the feelings or views of such a man may be on his first entering a neighbourhood, this truth is so well fixed in the minds of the surrounding families, that he is considered as the rightful property of some one or other of their daughters.</div>
</div>
```

<div style="height: 200px;
  line-height: 200px;
  border-color: black;
  border-width: 1px;
  border-style: solid">
  <div style="vertical-align: middle;
  display: inline-block;
  line-height: 1.5;
  text-align: justify">It is a truth universally acknowledged that a single man in possession of a good fortune must be in want of a wife. However little known the feelings or views of such a man may be on his first entering a neighbourhood, this truth is so well fixed in the minds of the surrounding families, that he is considered as the rightful property of some one or other of their daughters.</div>
</div>

不同于单行元素的居中，多行元素的line-height需要重置成较小的值，上面例子中取了文字的1.5倍，远小于父级元素的200px。将块级元素变成行内块级元素也是关键。如果仍旧只是块级元素，即使充值了line-height也不会生效，因为块级元素的一行会去和父级包含块的一行去居中对齐。

这种情况的应用场景极少遇到，恐怕只有按照列为主题布局时，才可能需要。

另外有一种通行的做法是将要居中的内容放置在一个单元格中，这种方案可以应对以上所有情况。

```css
#middleWrapper {
  height: 200px;
  line-height: 1.5;
  vertical-align: middle;
  display: table-cell;
  border-color: black;
  border-width: 1px;
  border-style: solid;
}
```

```html
<div class="middleWrapper">
It is a truth universally acknowledged that a single man in possession of a good fortune must be in want of a wife. However little known the feelings or views of such a man may be on his first entering a neighbourhood, this truth is so well fixed in the minds of the surrounding families, that he is considered as the rightful property of some one or other of their daughters.
</div>
```

<div style="height: 200px;
  line-height: 1.5;
  vertical-align: middle;
  display: table-cell;
  border-color: black;
  border-width: 1px;
  border-style: solid">
It is a truth universally acknowledged that a single man in possession of a good fortune must be in want of a wife. However little known the feelings or views of such a man may be on his first entering a neighbourhood, this truth is so well fixed in the minds of the surrounding families, that he is considered as the rightful property of some one or other of their daughters.
</div>

此外，还有一些方法也可行，但显得不够优雅，比如设定上下的padding为某个固定值可以应对某些情形等。
