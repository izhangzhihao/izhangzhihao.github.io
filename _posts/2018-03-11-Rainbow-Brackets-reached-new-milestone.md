---
layout: post
title: Rainbow Brackets has reached the milestone of 100000+ downloads and 200+ stars
categories: Intellij Kotlin
description: Rainbow Brackets 下载量超过 100000+ 了，并且在 github 上获得了 200+ stars
keywords: Rainbow Brackets, Intellij, Kotlin
---

## Milestone Reached

截止今天jetbrains 统计的下载量：101517

Github 上的 Stars：203

Github 上的 Issues & Pull requests：27 & 8

从第一个版本到现在总共发布了25个 release

Twitter 上还有不少人发推赞叹这个插件...

<blockquote class="twitter-tweet" data-lang="zh-cn"><p lang="en" dir="ltr">This is so the best thing that has ever happened to my IDE. Complex math lines will no longer haunt me in the middle of the night.<br><br>Rainbow Brackets plugin. Go get it. Now. <a href="https://t.co/7XAjYo8tYt">pic.twitter.com/7XAjYo8tYt</a></p>&mdash; Aerin Night (@AerinNight) <a href="https://twitter.com/AerinNight/status/946459777990721536?ref_src=twsrc%5Etfw">2017年12月28日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

甚至 @PhpStorm官方还好几次发推：

<blockquote class="twitter-tweet" data-conversation="none" data-lang="zh-cn"><p lang="en" dir="ltr">Rainbow Brackets plugin is available for installation via Plugins &gt; Browse Repositories for a long time already.</p>&mdash; PhpStorm (@phpstorm) <a href="https://twitter.com/phpstorm/status/949344231113535490?ref_src=twsrc%5Etfw">2018年1月5日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

在 Jetbrains plugin 上获得的好评：

* [Absolutely Brilliant. Now I wonder how I managed to code without it. On: IntelliJ Idea + material-theme](http://plugins.jetbrains.com/plugin/10080-rainbow-brackets#comment=25848)

* [Awesome plugin, can't live without it!](http://plugins.jetbrains.com/plugin/10080-rainbow-brackets#comment=24896)

* ...

## 一切的开始

在十一假期的时候抽了上午半天时间搞了个 IntelliJ 的插件，当时还写了博客[在这里](/2017/10/04/Intellij-rainbow-brackets-插件/)。
最开始也只是觉得好玩，写这个插件的目的也就是“自用”，然后本着“写了就写了顺便发到 jetbrains plugins 的仓库里面吧，万一有人用呢！”的精神，在当天做了一个 initial release。

## 社区贡献

这个插件能一直坐下来很大程度上要感谢社区的贡献，如果没有社区贡献可能早就停更了。来自开源社区的贡献包括但不限于：

* Pull request
* Defect
* Feature request
* Proposal
* ...

看了一下，issues 和 Pull requests总共有27 + 8个。这里尤其要感谢@YiiGuxing，在这个[pr](https://github.com/izhangzhihao/intellij-rainbow-brackets/pull/25)中，通过迁移解决了旧版本中每个语言都要有一个特殊实现的问题，整体简化了 plugin 的代码，真是棒棒哒。

## 收获

在维护这个插件的过程中还是有不少收获的

1. 对 Kotlin 有了一定了解。这个插件选 Kotlin 的原因很简单：1. 肯定要避免 Java 啊。 2. 基于 IntelliJ 的 IDE 都内置了 Kotlin 的 Runtime。所以就用了 Kotlin。在写这个插件之前学过一点 Kotlin，当时感觉是一个还不错的语言，在我心中的位置基本上就是“Java++”和“Scala--”。不过实际项目上没用过，也没特别仔细研究。在写这个插件的过程中也更好的了解了 Kotlin，算是比较大的收获吧。
2. Say no。我记得有不少于三个 issue 是要加对诸如`if`,`else`,`elif`等关键字的层级着色。这个我考虑了一下，因为几点原因没有加进来：1. 这个插件不是给诸如`if`,`else`,`elif`等关键字着色的，虽然跟对括号着色很像，但不是一回事。2. 这个功能其实不好做，因为几乎每种语言的`if`,`else`,`elif`都不一样，也就是说要为每种语言都实现一遍，这样很痛苦。然后这几个 issue 我就 close 了。如果有时间的话还是可以考虑另外起一个项目专门做这个的。
3. ...

## 期待新的 milestone

现在还差几个 setting page 没有做，第一个就是能自定义括号的颜色，第二个就是能控制 rainbow-ify 的级别。有时间了就看一下，除了设置之外也没发现什么 bug 了。这个插件还是会继续维护下去的，期待下一个 milestone 吧。