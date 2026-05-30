---
title: "CSS基本语法的总结"
date: 2011-01-07
categories:
  - "engineering"
tags:
  - "软件开发"
---

今天系统的学习了一下CSS(Cascading Style Sheets)的语法。之前看书时候感觉很乱但最后总结下来其实也就两个大的方面。现在记录下来以便帮助以后回顾。

### **CSS代码放置的位置**

**1\. 内联式样式表**

这里是指的每个html标签都有一个基本属性style，直接可以利用style设置该标签的样式。例如：

> <p style="font-family:Times New Roman; color:blue">content with decoration</p>

**2\. 嵌入式样式表**

要对同一个页面中某种标签都产生统一的样式，而不必要采用上面的第一种方式，提高了代码的重用性。这种方式需要将代码放置在页面的head标签中，如下：

> <head> <style type="text/css"> <!-- p { font-family: Times New Roman; color: blue; } //--> </style> </head>

按这种方式定义的只要该页面中使用<p></p>标签，则会使用该样式，除非定义自身的style属性则会覆盖全局的设定。

**3\. 外部样式表**

为了进一步提高代码的复用性，有时候许多的页面会采用同样的样式，这样就有必要将相同的样式部分提取成一个专门的叠层样式文件，后缀名通常为.css。如果要在某个html中使用它，则需要用如下的语句包含进去，并且放置在<head></head>中：

<link rel="stylesheet" type="text/css" href="外部css文件名">

**4\. 输入样式表**

这种方式是提供css文件中包含另外一个css的语法。当然也能够在html的嵌入式样式定义中使用。使用方法就是包含如下的语句：

> @IMPORT url("外部css文件名");

### **样式选择器**

样式选择器一般说是六种，实际个人感觉就是四种基本的加上两种不同的复合定义方式。样式的定义本身就是属性的关键字style。后来为了提供全局的加入了style标签。但是无论采用上面提到四种方式的哪一种都只是针对具体某个标签或者全部的某种标签。例如页面中有十个p标签，如果想让其中4个有一组修饰样式，另外6中为另外一种修饰样式，则只能单个的定义每个p的style属性。代码量最少也就是定义一个全局的控制其中6个一致的，剩余的4个分别定义。但引入class样式选择器后上面的问题的解决就方便的多，只需要定义两个。css样式选择器使得样式的定义方式多种多样，这里就不做过多的个人体会了，重点说总结。样式选择器的六种分别是：

**1\. html选择器**

这个是最基本的css修饰的对象，即每个body以内的html标签一般都可以修饰。

**2\. class选择器**

每个html标签基本都有一个class属性使得它们即便标签名不同，也能够具有同样的修饰样式。

- ? 如果针对的是某中标签，则为如下定义方式：

> <style type="text/css"> <!-- p.tradition { font-family: Times New Roman; color: blue; } //--> </style>

使用时

> <p class="tradition">content with decoration</p>

- ? 如果要想让不同标签名的标签具有相同的修饰方式，只需以点开始定义样式，如下：

> <style type="text/css"> <!-- .tradition { font-family: Times New Roman; color: blue; } //--> </style>

使用时

> <p class="tradition">content with decoration</p> <a class="tradition" href="index.html">content with decoration</a>

**3\. id选择器**

id选择器也是利用每个标签的另外一个属性，id. 进行定义，类似于class，但需要以进号开头，由于id名的唯一性，通常id用于定义div标签中整个区域块的修饰。定义方式：

> <style type="text/css"> <!-- #top{ font-family: Times New Roman; color: blue; } //--> </style>

引用时

> <div id="top"></div>

**4\. 伪元素选择器**

有些标签不同属性代表不同的状态，也可以具有当前状态的修饰，常用的就是链接标签a. 如下面的定义

> <style type="text/css"> <!-- a:link { text-decoration: none; font-family: Times New Roman; color: blue; }
>
> a:hover { font-weight: bold; font-family: Times New Roman; color: blue; } //--> </style>

**5\. 关联选择器**

关联选择器相当于对标签之间做了次且运算，只有按照定义标签之间的组合顺序才能够正确的应用该定义的样式，比如下面的定义：

> <style type="text/css"> <!-- center h1 em { font-family: Times New Roman; color: blue; } //--> </style>

上面的组合情况说明只有center标签里面有h1标签，并且h1中套用了em标签的情况下才能正确的应用该样式。如下的使用：

> <center><h1>This is a <em>test</em></h1></center>

注意上面例子的显示结果只有test是蓝色的。另外需要注意的是几个标签之间都是空格分隔。

当然关联也不止局限于html标签，当然也可以是自己定义class标签和id标签。如下的定义：

> <style type="text/css"> <!-- #tradition .top em { font-family: Times New Roman; color: blue; } //--> </style>

使用规则还是一样，必须同时满足定义顺序的才可以生效。

**6\. 组合选择器**

组合选择器相当于同一样式应用于不同标签，或者不同类别，不同id的简化定义。各个标签、class名或者id名之间使用逗号，即","，隔开。应用方式如下：

> <style type="text/css"> <!-- #tradition, .top, em, h1 { font-family: Times New Roman; color: blue; } //--> </style>
