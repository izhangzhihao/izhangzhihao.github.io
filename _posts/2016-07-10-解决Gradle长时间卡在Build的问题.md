---
layout: post
title: 解决Gradle长时间卡在Build的问题
description: 解决Gradle长时间卡在Build的问题
---
# 今天改了一下项目依赖，结果gradle一直卡在build！卡了两个小时左右吧，看了个电影回来都没有什么改观... ...

Google的时候发现了这个https://teamtreehouse.com/community/android-studio-is-stuck-on-gradle-initalise-build-running-as-administrator-and-ive-let-it-through-my-firewall
 
 # 以管理员身份启动IDEA就好了！！！

 # 还有一个原因，我配置的依赖版本开源中国镜像上没有，所以卡死了，要么换旧版，要么用中心仓库
