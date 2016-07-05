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
            <driverClass>com.mysql.jdbc.Driver</driverClass>
            <url>jdbc:mysql://10.16.155.241:8081/Log?useUnicode=true&amp;characterEncoding=utf-8&amp;serverTimezone=UTC&amp;useSSL=false</url>
            <user>root</user>
            <password>root</password>
        </connectionSource>
        <sqlDialect class="ch.qos.logback.core.db.dialect.MySQLDialect" />
        <insertHeaders>true</insertHeaders>
    </appender>
    <!-- 为某个包下的所有类指定Appender 也可以指定类名称-->
    <!--<logger name="com.zhangzhihao.SpringMVCSeedProject">
        <appender-ref ref="stdOut"/>
        <appender-ref ref="db-classic-mysql"/>
    </logger>-->

    <logger name="com.zhangzhihao.SpringMVCSeedProject" level="INFO" additivity="false">
        <appender-ref ref="db-classic-mysql"/>
    </logger>
    <!--TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF-->
    <root level="INFO">
        <appender-ref ref="stdOut"/>
        <appender-ref ref="db-classic-mysql"/>
    </root>
</configuration>


```
---
