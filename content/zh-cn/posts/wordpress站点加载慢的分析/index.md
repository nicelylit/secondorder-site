---
title: "WordPress站点加载慢的分析"
date: 2015-01-08
categories: 
  - "个人博客"
---

Chrome浏览器或者火狐浏览器按Ctrl+Shift+I，打开开发者工具，选择Network，可以用来分析加载网页时到底哪些链接拖慢了加载速度。以火狐浏览器为例，可以看到以下的分析结果： [![slow\_analysis\_1](/wp-content/uploads/2015/01/slow_analysis_1-610x255.png)](/wp-content/uploads/2015/01/slow_analysis_1.png)

拖慢整个节奏的是来自域名fonts.googleapis.com的请求，无法解析的原因不言自明。找到原因后去相应的文件中修改代码即可解决问题。我站点使用模板的问题出现在style.css中，不同的模板会有不一样的变化，有了开发者工具，可以灵活应对。网上多有记录一般引起站点加载慢除了站点模板中有用google服务外，还有avatar也是一大类，这个可以通过安装Simple Local Avatar插件解决。修改后，加载时间立刻从40s减到了3.84s。 [![slow\_analysis\_2](/wp-content/uploads/2015/01/slow_analysis_2-610x255.png)](/wp-content/uploads/2015/01/slow_analysis_2.png) 由于模板来自网上，加之自己对前端也不太熟，看这个分析报告，可优化的代码还有很多，以后慢慢改吧。另外，以下的这张图显示Chrome加载速度略快。 [![slow\_analysis\_3](/wp-content/uploads/2015/01/slow_analysis_3-610x255.png)](/wp-content/uploads/2015/01/slow_analysis_3.png)
