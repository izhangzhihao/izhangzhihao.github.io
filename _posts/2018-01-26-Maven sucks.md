---
layout: post
title: Convert maven project to gradle project
categories: gradle maven
description: Maven太恶心啦，我要用 Gradle !
keywords: maven, gradle
---

# 快速把 maven 项目转为 gradle 项目！

只需要在 maven 项目根目录运行

``` bash
gradle init
```

就可以了！gradle 会自动根据 pom 文件生成 build.gradle 文件，经测试，包含多个子项目的 maven 项目也可以正确的转换为 gradle 项目！

## 插播一段官方文档：

[Build Init Plugin](https://docs.gradle.org/current/userguide/build_init_plugin.html):

The Gradle Build Init plugin can be used to bootstrap the process of creating a new Gradle build. It supports creating brand new projects of different types as well as converting existing builds (e.g. An Apache Maven build) to be Gradle builds.

## 然后做什么？

卸载 maven 啊

``` bash
brew uninstall maven
```

以后只用 gradle 和 sbt 了。