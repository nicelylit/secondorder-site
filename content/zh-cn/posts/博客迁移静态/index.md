---
title: "博客迁移静态"
date: "2026-06-10T21:16:00+08:00"
lastmod: "2025-06-13T22:03:00+08:00"
categories:
  - "engineering"
tags:
  - "个人博客"
image: "nicelylit_a_cinematic_editorial_header_illustration_about_mig_98437f1d-37f6-4398-8d6a-e2576910fa1e_0.png"
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

Hugo使用markdown，相较而言，内容编辑比较容易。不管直接用文本编辑器，还是用Obsidian编辑渲染一体的方式，都可以把重点放在文字上，而非折腾样式上。

对于特殊样式的支持，也有两个层级，单篇文章和整站。单篇文章中能通过临时写HTML，整站可以把HTML做成模板放到layouts中。通过AI的指点，Hugo的模板语言虽然晦涩，模板覆盖规则虽然繁琐，但基本都能找对，并改到满意的程度。

举例来说，要控制列表页摘要的长度，单篇文章在需要截断的地方加&lt;!--more--&gt;，整站在config.yaml中修改`summaryLength: 80`。

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

在`content/posts`写完文章，运行`hugo server -D`可以生成HTML。在浏览器中预览效果，跟在远程几乎没有差别。

发布到远端的对象存储（比如阿里云OSS）前，运行`hugo --minify`即可，产出的`public`目录会有压缩过的CSS和JavaScript。

每次手工上传`public`目录还比较繁琐，可以用命令行工具，比如OSS提供的`ossutil`工具上传同步：

```bash
ossutil sync -f public/ oss://secondorder-site/ --delete
```

将这个下载命令行工具、执行命令的工作，配置成GitHub Action，可以在每次推送新提交的时候触发执行，参考实现[.github/workflows/deploy-oss.yml](https://github.com/nicelylit/secondorder-site/blob/main/.github/workflows/deploy-oss.yml)。其中的secrets变量，要在[Actions secrets and variables](https://github.com/nicelylit/secondorder-site/settings/secrets/actions)中配置。执行历史，可以在[Actions页面](https://github.com/nicelylit/secondorder-site/actions/runs/27456684672)查看。某个提交跳过Action，可以在提交消息中加上`[skip ci]`。

站点部署的维护性工作，还有一块，就是域名和证书的刷新。

为了保持良好的访问性能和适当的安全性，我开启了OSS bucket的private模式，然后开通了CDN的域名加速。

域名加速时用443端口，需要部署SSL证书。目前的商业DV证书都很贵，对于没什么影响力的个人博客，不划算。好在还有免费的证书可选，但免费证书有效期最长只有三个月，每次去重新申请、上传也是件说大不大，但令人繁琐的事情，索性也把这个过程给自动化了。

GitHub Action不仅支持推送提交时触发执行，还支持执行周期性的定点任务。基本思路是每天检查当前证书是否过期，如果离过期还有7天，那么就生成新的证书替换，并发送提醒，如果超过7天，就自动跳过。考虑到GitHub Action每天执行的服务器并不固定，存储本地目录的证书无法被用来做过期检查的判断条件，于是只能使用CDN的API `DescribeDomainCertificateInfo`，读取线上证书的元信息，进行判断。完整实现参考[.github/workflows/renew-cdn-cert.yml](https://github.com/nicelylit/secondorder-site/blob/main/.github/workflows/renew-cdn-cert.yml)。

## 托管互动便于运营

博客虽然比内容平台发布内容更灵活，形式自由，内容没有人工审核，只有大众监督，但也少了便利的流量和互动运营支撑条件。

我的SEO经验不是太丰富，之前内容也都写得比较晦涩和小众，不利于传播。单篇累积阅读量也就几千，还不如我一篇小红书笔记，其中甚至还有不少搜索引擎机器人的访问。评论就更是少得可怜，大概只有特别触动人，或者具有争议性的东西，才会引发评论。我写了几十篇，留下有效评论的，也就10条以内吧，却还要面对上万垃圾评论，实在是没必要花心思。

基于这个经验，索性不再追求实时在页面上显示阅读数，使用百度统计，看个大概趋势即可。只要文章的URL是固定链接，这个数据就会是稳定和有参考意义的。百度统计的集成很简单，只需要注册个账号，登记一下域名，把生成的JavaScript脚本加到每个页面头部就可以了。创建`layouts/partials/head/custom.html`，把脚本放里面，具体参考[这个提交](https://github.com/nicelylit/secondorder-site/commit/3f13c7199799efd488e1c93539e06e16e830eaa0)。

对于评论，麻烦在于垃圾评论治理。要限制垃圾评论，就要有审核机制。有审核机制通常需要账号系统，虽然用AI和开源的系统可以很快搭出来，但想想这个运营成本，只会比CleanTalk更高。我目前的站点情况，应该把精力放在内容创作和内容运营上。权衡下来，采用了giscus的方案，就是利用GitHub的Discussions板块，需要接受留言的用户要有GitHub账号授权。这虽然大大限制了留言的人群，但也跟我的网站比较契合，筛选掉了不相干的人。何况WordPress开放评论，也没几个评论。

有了GitHub账号的用户，留言也还是挺方便的，点击留言，首次会触发打开GitHub账号授权页面，登录授权后，有一定时间的缓存不用再次登录，授权是永久的。

每篇文章的留言，会在[Discussions](https://github.com/nicelylit/secondorder-site/discussions)里面新建一个楼层，giscus脚本会通过API去读取。看我的[测试情况](https://github.com/nicelylit/secondorder-site/discussions/1)，中文链接虽然是编码的格式，但评论是可读的。在页面上，评论区懒加载，并不会因为国内访问GitHub不稳定而影响页面整体加载速度。

我用的stack主题添加评论区很简单，只需要在`config.yaml`中，增加`comments`部分就可以了。不过这部分的信息，需要GitHub安装[giscus应用](https://github.com/apps/giscus)。之后打开[配置页面](https://github.com/settings/installations/137494193)，填写评论关联哪个Discussion话题，以及有哪些评论偏好。填写完毕，会生成一段脚本，把里面的参数值加到`comments`中即可，最终配置参考[config.yaml](https://github.com/nicelylit/secondorder-site/commit/6392af0565af8711c28341852c18c0a92b8392c0)。

## 去主机服务降低成本

静态网站的方案，去掉了每年500元的独享虚拟主机基础版，也省掉了几百元的商业SSL证书。

新增加的成本是OSS的存储和访问成本，还有CDN的访问成本。OSS存储也就100多兆，忽略不计，OSS回源走内部网络，不计费。

[CDN访问成本](https://www.aliyun.com/price/detail/cdn)，下行数据，一个月10TB以内，0.24元/GB，总流量10GB，一个月不到5元，一年最多60元。

从5月31日配好，目前快半个月了，流量430MB，远低于10GB。为防止恶意攻击，在CDN上，设置了流量限制，一个月超过60GB，自动关停。