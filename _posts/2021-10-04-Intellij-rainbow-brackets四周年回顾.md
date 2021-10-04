---
layout: post
title: IntelliJ Rainbow Brackets四周年回顾
categories: Intellij
description: 如何运营一个开源项目
---

## 前情提要

2017年十一假期时闲来无事搞点事情，花了一上午时间做了一个intelliJ插件[Rainbow Brackets](https://github.com/izhangzhihao/intellij-rainbow-brackets)，这是一个为嵌套的多层括号着色的插件，能够让代码更易读、更容易发现错误。当天下午就发了第一个release版本，到今天正好四年整。本文主要是回顾这个开源项目至今的成就&总结运营开源项目经验。

## 成就篇

* Github stars: 3600+

* commits: 800+

* Feature requests: 54

* Releases: 91

* 支持编程语言: 30+(几乎所有的主流语言都支持)

* 支持IDE: Jetbrains全系(除了CodeWithMe Client) + Android Studio + HUAWEI DevEco Studio

* 下载量: 560万+

* 评分: 4.9(满分五分) 评价146次

* 第三方插件下载量排名: 8

* 接受捐赠：$4000+ 微信&支付宝的收款统计功能不大好用，毛毛估￥1000以内

* 捐赠人数: 60+ (微信&支付宝未统计在内)

* Project Sponsor: 2([Tabnine/Codota](https://www.codota.com/?utm_source=jbmarket&utm_campaign=jbrainbowbrackets&utm_medium=banner) & [Codestream](https://sponsorlink.codestream.com/?utm_source=jbmarket&utm_campaign=jbrainbowbrackets&utm_medium=banner)), 两位sponsor合计捐赠约$3000

* 被Jetbrains twitter推荐：8次+

## 运营篇

有了上述的成就，再来展开下如何维护并运营好一个开源项目

### “开源”不仅仅是一个public github repository

我认为最基本的要求应该包括更多但不限于：

* 应该尽可能使用英文而不是中文（如果你不是只想要中文用户的话）

* 要有README，好让别人知道这是一个什么项目

* 合适的license（比如Apache License等）

* 正式的release发布（比如发布到maven central等官方仓库），同时应该有[release change log](https://github.com/izhangzhihao/intellij-rainbow-brackets/releases/tag/6.21)

* 版本号约定(比如follow广受认可的[Semantic Versioning](https://semver.org/))，其中隐含了向前、向后兼容性保证和bug修复等语义

* CI

* issue/pull request管理，回答问题、review代码等

### 更重要的是...

* 能够解决真实世界的问题。能够解决问题是最基本、最重要的价值，这样才会有意义，才能吸引用户

* 高质量的实现。包括但不限于：

    *  实现有无bug
    *  尽量少的依赖
    *  清晰的API定义
    *  易于扩展的接口设计
    *  测试覆盖
    *  等

* 应该有完整的文档，包括[如何使用](https://github.com/izhangzhihao/intellij-rainbow-brackets#use-color-generator)、[如何构建](https://github.com/izhangzhihao/intellij-rainbow-brackets#contribute)、[如何贡献](https://github.com/izhangzhihao/intellij-rainbow-brackets#support-us)、截图、example、quick start guide等

* 构建自己的社区

    * 及时回复issue、pull request，或者帮助用户解决问题
    * 透明，作出决定时解释缘由（比如为什么我们要加这个功能或者为什么不加这个功能）
    * 文档(again)

* 广而告之（不是指Google ads那种广告，而是通过SEO(Search Engine Optimization)的手段让其他人更容易找到），这里就有几个方式：

    * 项目名称非常重要，最好让人一眼就能够get到它是做什么的，并且不与其他项目的名字冲突（例如Rainbow Brackets首先与VS Code同名插件功能相同，前面加上intelliJ，可以让人知道这是一个在inteliJ平台上工作的插件）
    * 同时要有简短的介绍，就算没get到名字的含义，通过介绍也能知道
    * 关键词（例如在README里面不厌其烦的列出来关联的[语言](https://github.com/izhangzhihao/intellij-rainbow-brackets#supported-languages)，[产品](https://github.com/izhangzhihao/intellij-rainbow-brackets#compatibility)，[其他插件](https://github.com/izhangzhihao/intellij-rainbow-brackets#authors-choice)等，或者通过github topic与其他项目关联到一起）
    * 链接（相关产品、竞品链接等）

* 捐赠

    项目刚开始时是可能是因为好玩、感兴趣、自己要用等等原因，大多数这些都是短时间的。过了起步阶段之后能持续维护可能是因为好评、用户、下载等指标带来的成就感。当项目逐渐稳定后，可能更多的是要修复一些边边角角的bug，甚至可能是我用不到的功能的bug。这时可能就会变得枯燥乏味，用爱发电就不可持续了。想要更长期的维护好一个项目，我认为要么需要有其他的contributor进来一起维护项目，要么就得有捐赠来让维护者更有动力继续。捐赠的钱不必很多，更多的是对业余时间的一些小小补偿，买个啤酒、咖啡、吃顿饭之类。

* 明确定位

    项目逐渐开始做大之后，多少会有一些feature request。有些可能比较make sense，有些可能多少沾点边，有些可能比较偏。如果你不想做一个万能工具箱的话，就需要拒绝一些feature request了，而不是统统接受，保持定位清晰也有助于其他人快速知道该项目的价值。当然一些很有价值的feature request或者想法可以积极采纳或者寻求社区的contribution(如果你精力有限)。

### 懂得了以上的策略并不代表你的开源项目能够成功

开源社区只会锦上添花而不会雪中送炭，绝大部分项目都默默无闻。不要想着放一个半成品到github就会有用户或者有人贡献代码(除非你的作品非同一般，完全没有替代品)，我的经验看来绝大部分人都是伸手党，能够正确报bug的人都很少，更不必谈给你贡献代码了。但是当项目做大就不一样了，当功能逐渐完整，有了用户群体，就会有人来解决力所能及的问题。孤军奋战才是更加真实的写照

![](https://brunocapuano.files.wordpress.com/2015/12/strip-vision-open-source-650-finalenglish.jpg)


## 最后

不要把敏感信息提交到公共GitHub！
    
***本文主要参考自本人的经验，尽管在完成此文前已经尽力回想了一些技巧，但很有可能仍有疏漏，并且这些技巧也不能保证项目一定成功。***