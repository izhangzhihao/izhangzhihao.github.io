---
layout: post
title: Oozie 调度 Sqoop 从 Mysql 中导数据到 Hive 遇到的坑
categories: 大数据
description: Oozie 调度 Sqoop 从 Mysql 中导数据到 Hive 遇到的坑
keywords: Oozie,Sqoop,Hive
---

# Oozie 调度 Sqoop 从 Mysql 中导数据到 Hive 遇到的坑

## 找不到数据库驱动

``` java
java.lang.RuntimeException: Could not load db driver class: com.mysql.jdbc.Driver
	at org.apache.sqoop.manager.SqlManager.makeConnection(SqlManager.java:875)
    ...    
```

解决方法：把 mysql 的 connector 的 jar 包丢到HDFS里面，路径在`${nameNode}/user/oozie/share/lib/sqoop`。

然后在“job.properties” 中加上：

``` config
oozie.libpath=${nameNode}/user/oozie/share/lib/sqoop
```

## Could not start Java compiler

在 `sqoop` 用户中直接跑命令没问题，在 oozie 中居然挂了

``` java
2183 [main] ERROR org.apache.sqoop.tool.ImportTool  - Encountered IOException running import job: java.io.IOException: Could not start Java compiler.
	at org.apache.sqoop.orm.CompilationManager.compile(CompilationManager.java:192)
    ...
```

解决办法：在`oozie`用户下设置`JAVA_HOME`:

``` bash
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.151-1.b12.el7_4.x86_64
```

## Heart beat

``` log
...
5732 [main] INFO  org.apache.hadoop.yarn.client.api.impl.YarnClientImpl  - Submitted application application_1513839281144_0004
5752 [main] INFO  org.apache.hadoop.mapreduce.Job  - The url to track the job: http://node3.domain:8088/proxy/application_1513839281144_0004/
5752 [main] INFO  org.apache.hadoop.mapreduce.Job  - The url to track the job: http://node3.domain:8088/proxy/application_1513839281144_0004/
5752 [main] INFO  org.apache.hadoop.mapreduce.Job  - Running job: job_1513839281144_0004
5752 [main] INFO  org.apache.hadoop.mapreduce.Job  - Running job: job_1513839281144_0004
Heart beat
Heart beat
Heart beat
Heart beat
Heart beat
Heart beat
Heart beat
Heart beat
Heart beat
...
```

然后“Heart beat”就停不下来了...

看起来像是在等待什么东西，或者因为资源争夺死锁了。

试了很多办法，比如：增加 queue 的数量，指定在哪个 queue 中跑 job ... 都没能解决。

机器内存配置：8G、12G、12G

然后[这里](https://community.cloudera.com/t5/Batch-Processing-and-Workflow/Oozie-sqoop-action-in-CDH-5-2-Heart-beat-issue/m-p/23494/highlight/true#M869)有在讨论 Heart beat issue。有个评论说***"By default Hadoop Total memory size was allot as 8GB.So if we run two mapreduce program memory used by Hadoop get more than 8GB so it met deadlock."***

而且每次使用 oozie 的 workflow 提交的时候都会在 hadoop 上跑两个 “app“。而且我把`yarn.scheduler.maximum-allocation-mb`设置到了8G，原因是之前出现过一次

``` log
2017-12-20 21:33:58,261 INFO [AsyncDispatcher event handler] org.apache.hadoop.mapreduce.v2.app.job.impl.TaskAttemptImpl: Diagnostics report from attempt_1513820237394_0011_m_000000_0: Container killed by the ApplicationMaster.
Container killed on request. Exit code is 143
Container exited with a non-zero exit code 143
```

的问题，我找到了[这个](https://community.hortonworks.com/questions/96183/help-troubleshoot-container-killed-by-the-applicat.html)。对比两个问题，应该是`yarn.scheduler.maximum-allocation-mb` 相对太大，`yarn.nodemanager.resource.memory-mb`相对太小也是8G, 结果一个 app 就占满了所有可用内存，其他的 app 无法运行，把`yarn.scheduler.maximum-allocation-mb`设置为2G 就能解决问题了。再次跑 oozie job 的时候就可以在[cluster](http://node3.domain:8088/cluster)中看到有两个 app 同时运行了。

## 广告时间

Oozie居然用 XMl 来定义各种 job，写 XML 实在是太痛苦啦！！！
然后就有了[soozie](https://github.com/izhangzhihao/soozie),一个在 Oozie 之上的 scala DSL。
然后就可以这样写

```scala
val first = MapReduceJob("first") dependsOn Start
val second = MapReduceJob("second") dependsOn first
val third = MapReduceJob("third") dependsOn second
val fourth = MapReduceJob("fourth") dependsOn third
val end = End dependsOn fourth
```

最后调用`writeJob`方法生成对应的 XML 和目录结构。

[soozie](https://github.com/izhangzhihao/soozie)有以下好处：

* 方便复用，以后再也不需要复制粘贴 XML，出了问题到处改啦
* 拼写检查，标签写错了？不可能！
* 类型安全，在错误的地方传了错误的参数这种情况不会再出现啦，感谢编译器
* 可读性更好，XML 实在恶心