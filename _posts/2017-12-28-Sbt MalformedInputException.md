---
layout: post
title: Fix MalformedInputException
categories: sbt
description: Fix sbt 运行测试时遇到的java.nio.charset.MalformedInputException Input length = 1问题
keywords: scala,MalformedInputException,Input length = 1,sbt
---

## 今天跑测试的时候遇到了`java.nio.charset.MalformedInputException: Input length = 1`异常。

错误爆在一行读包括中文字符文件的代码上，猜想应该是编码问题。然后搜到了[这个](https://github.com/sbt/sbt-header/issues/56),其中@ignasi35有条回复说道"Turns out the updated plugin in IntelliJ was resolving LC_CTYPE="C" causing the JVM to pick the file.encoding == US-ASCII. Setting LC_CTYPE="UTF-8" on my .bash_profil solved the issue in all my run environments."

所以直接在.bash_profil中添加：

```
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
export LANGUAGE=en_US.UTF-8
```

然后`source ~/.bashrc`,再跑，问题解决。