---
layout: post
title: 使用LogBack将日志记录到MySql数据库
categories: LogBack
description: 使用LogBack将日志记录到MySql数据库
---
# 第一步 数据库建表
## 在数据库执行以下语句建表

---
``` Sql

DROP TABLE IF EXISTS `logging_event`;
CREATE TABLE `logging_event` (
  `timestmp` LONG NOT NULL,
  `formatted_message` TEXT NOT NULL,
  `logger_name` VARCHAR(255) NOT NULL,
  `level_string` VARCHAR(255) NOT NULL,
  `thread_name` VARCHAR(255) NOT NULL,
  `reference_flag` SMALLINT NOT NULL,
  `caller_filename` VARCHAR(255) NOT NULL,
  `arg0` VARCHAR(255),
  `arg1` VARCHAR(255),
  `arg2` VARCHAR(255),
  `arg3` VARCHAR(255),
  `caller_class` VARCHAR(255) NOT NULL,
  `caller_method` VARCHAR(255) NOT NULL,
  `caller_line` VARCHAR(255) NOT NULL,
  `event_id` INT NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (event_id)
) ENGINE=InnoDB DEFAULT CHARSET=UTF8;




DROP TABLE IF EXISTS `logging_event_property`;
CREATE TABLE `logging_event_property` (
  `event_id` INT NOT NULL,
  `mapped_key` VARCHAR(255) NOT NULL,
  `mapped_value` TEXT NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=UTF8;




DROP TABLE IF EXISTS `logging_event_exception`;
CREATE TABLE `logging_event_exception` (
  `event_id` INT NOT NULL,
  `i` SMALLINT NOT NULL,
  `trace_line` VARCHAR(255) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=UTF8;

```
---

# 第二步 添加依赖项 以下是Gradle依赖项

---
``` groovy

dependencies {
    compile(
            "org.slf4j:slf4j-api:1.7.21",
            "org.slf4j:jcl-over-slf4j:1.7.21",//jcl-over-slf4j 把 Commons-Logging 桥接到 Slf4J，然后 Logback。
            "ch.qos.logback:logback-core:1.1.7",
            "ch.qos.logback:logback-classic:1.1.7",
            )
}

```
---

# 第三步 配置logback.xml

---
``` xml

<configuration scan="true" scanPeriod="60 seconds" debug="false">

    <appender name="stdOut" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{40} - %msg%n</pattern>
        </layout>
    </appender>

    <appender name="db-classic-mysql" class="ch.qos.logback.classic.db.DBAppender">
        <connectionSource class="ch.qos.logback.core.db.DriverManagerConnectionSource">
            <driverClass>com.mysql.cj.jdbc.Driver</driverClass>
            <url>jdbc:mysql://10.16.155.241:8081/SpringMVCSeedProject?useUnicode=true&amp;characterEncoding=utf-8&amp;serverTimezone=UTC&amp;useSSL=false</url>
            <user>root</user>
            <password>root</password>
        </connectionSource>
    </appender>

    <logger name="org" level="ERROR" additivity="false">
        <appender-ref ref="db-classic-mysql"/>
    </logger>

    <logger name="com" level="ERROR" additivity="false">
        <appender-ref ref="db-classic-mysql"/>
    </logger>

    <logger name="java" level="ERROR" additivity="false">
        <appender-ref ref="db-classic-mysql"/>
    </logger>

    <logger name="javax" level="ERROR" additivity="false">
        <appender-ref ref="db-classic-mysql"/>
    </logger>

    <logger name="net" level="ERROR" additivity="false">
        <appender-ref ref="db-classic-mysql"/>
    </logger>

    <!--TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF-->
    <root level="WARN">
        <appender-ref ref="stdOut"/>
    </root>
</configuration>



```
---

如果不想使用xml配置，可以将配置写在logback.groovy里面，xml在线转groovy http://logback.qos.ch/translator/asGroovy.html 示例如下

---
``` groovy

import ch.qos.logback.classic.PatternLayout
import ch.qos.logback.classic.db.DBAppender
import ch.qos.logback.core.ConsoleAppender
import ch.qos.logback.core.db.DriverManagerConnectionSource

import static ch.qos.logback.classic.Level.ERROR
import static ch.qos.logback.classic.Level.WARN

scan("60 seconds")
appender("stdOut", ConsoleAppender) {
  layout(PatternLayout) {
    pattern = "%d{HH:mm:ss.SSS} [%thread] %-5level %logger{40} - %msg%n"
  }
}
appender("db-classic-mysql", DBAppender) {
  connectionSource(DriverManagerConnectionSource) {
    driverClass = "com.mysql.cj.jdbc.Driver"
    url = "jdbc:mysql://10.16.155.241:8081/SpringMVCSeedProject?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC&useSSL=false"
    user = "root"
    password = "root"
  }
}
logger("org", ERROR, ["db-classic-mysql"], false)
logger("com", ERROR, ["db-classic-mysql"], false)
logger("java", ERROR, ["db-classic-mysql"], false)
logger("javax", ERROR, ["db-classic-mysql"], false)
logger("net", ERROR, ["db-classic-mysql"], false)
root(WARN, ["stdOut"])


```
---

# 如果使用了Hibernate并且做了映射的话

---
```

第一步中最好将 `timestmp` LONG NOT NULL, 改为 `timestmp` mediumtext NOT NULL,
映射可以这么写 @Column(columnDefinition="MEDIUMTEXT") private String timestmp;

```
---
