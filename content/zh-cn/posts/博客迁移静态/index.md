---
title: "博客迁移静态"
date: 2026-06-10
lastmod: "2025-06-10T21:16:00+08:00"
draft: true
categories:
  - "engineering"
tags:
  - "个人博客"
---

从2014年12月开通博客以来，已经过去11年了，期间[做过3次大的维护]({{< relref "posts/博客维护日志/index.md" >}})：

- 2016年，希望获取内容反馈，定制主题，启动文章浏览次数
- 2020年，阿里云产品升级，万网关停X享主机-X3，被迫切换独享虚拟主机基础版
- 2023年，难以忍受垃圾评论，清理了两万多条，购买垃圾评论过滤服务，改善移动端体验

最近，网站页面有近50%的几率加载报错，管理员仪表盘打开也很慢，开始动了迁移博客的念头。

## WordPress变得难以忍受

难以忍受的事情远不止浏览体验，还有内容编辑、维护成本等，细细盘点起来，有下面这些：

- 买了3年的CleanTalk服务到期，垃圾评论又多了起来；
- WordPress不敢轻易升级，怕跟PHP环境和数据库版本不兼容；
- 插件升级经常性失败，也不敢强升；
- 本地开发测试环境跟线上运行环境有差异，对比调试费功夫；
- 站点部署没有自动化，代码库的版本跟线上跑的版本已经出现不同步；
- 定制逻辑没有自动的回归测试，升级覆盖了也不清楚；
- 古腾堡编辑器虽然比经典编辑器好用，但插入代码、目录、表格仍旧不理想；
- 博客想支持双语版，还得找新主题，定制成自己想要的风格，改起来非常花时间；
- 动态渲染逻辑复杂，页面加载速度慢，调试复杂；
- 独享虚拟主机基础版价格一年500元，关停的X享主机-X3一年只需要88元；
- 正经评论没几条，还得给垃圾评论过滤花钱，费心思；
- 评论区的用户头像外网服务已经无法访问，还没有替换方案。

## Hugo试着很流畅

[Hugo](https://gohugo.io)是一个静态网站生成器，会将markdown写的文章转换成HTML，配上CSS控制样式，以及JavaScript控制动画和延迟加载，完全不需要后台。前端资源通过CDN缓存加速，很容易做得流畅。

直接照着[快速上手文档](https://gohugo.io/getting-started/quick-start/)，试了试，运行`hugo server`会在本地启动一个服务器，辅助浏览和调试。

听了AI的推荐，也在[Themes市场](https://themes.gohugo.io)看了一圈，选择了stack主题，支持双语，风格符合我的审美。

## 写文章体验升级

WordPress文章编辑有版本控制，古腾堡也基本上是所见即所得，但想要插入代码块、特殊样式块，需要折腾插件，效果还难达预期。

markdown相较而言，内容编辑比较容易。不管直接用文本编辑器，还是用Obsidian编辑渲染一体的方式，都可以把重点放在文字上，而非折腾样式上。

对于特殊样式的支持，也有两个层级，单篇文章和整站。单篇文章中能通过临时写HTML，整站可以把HTML做成模板放到layouts中。通过AI的指点，Hugo的模板语言虽然晦涩，模板覆盖规则虽然繁琐，但基本都能找对，并改到满意的程度。

举例来说，要控制列表页摘要的长度，单篇文章在需要截断的地方加<!--more-->，整站在config.yaml中修改`summaryLength: 80`。

再比如，为了支持图片环绕文字，在`layouts/shortcodes/floatblock.html`中添加带`floatblock`样式的`div`标签，封装成可用模块。在文章中，使用时，直接引用`<floatblock>`即可，比如下面的例子：

```html{linenos=table}
{{- $pos := .Get "pos" | default "right" -}}
{{- $w := .Get "w" | default "320px" -}}
{{- $class := .Get "class" | default "" -}}

<div class="floatblock floatblock-{{ $pos }} {{ $class }}" style="--floatblock-w: {{ $w }};">
  {{ .Inner }}
</div>
```

```html{linenos=table}
{{</* floatblock pos="right" w="480px" class="brand-block" */>}}
<img src="/wp-content/uploads/2019/09/civilization-architecture-res.png" alt="logo" style="max-width:480px; margin: 0 auto 12px;">
<div class="brand-title">文明的体系结构 - 颜色越浅越虚幻，颜色越深越贴近无可言说之实在</div>
{{</* /floatblock */>}}
```

## 持续集成减轻维护成本

## 增强互动便于运营

## 降低成本