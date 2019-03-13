---
title: Java-Springboot-logback-xml配置文件详解
date: 2019-03-13 15:03:32
tags: SpringBoot全家桶
---
+ 依赖
```
<!-- springboot 的springboot-core已经依赖了logback-core和logback-classic -->
<dependency>
<groupId>org.slf4j</groupId>
<artifactId>slf4j-api</artifactId>
<version>1.7.25</version>
</dependency>
<dependency>
<groupId>org.slf4j</groupId>
<artifactId>log4j-over-slf4j</artifactId>
<version>1.7.25</version>
</dependency>
```
+ Spring Boot应用将自动使用logback作为应用日志框架，Spring Boot启动的时候，由org.springframework.boot.logging.Logging-Application-Listener根据情况初始化并使用。
+ 日志级别
从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出。
+ 自定义日志配置
由于日志服务一般都在ApplicationContext创建前就初始化了，它并不是必须通过Spring的配置文件控制。因此通过系统属性和传统的Spring Boot外部配置文件依然可以很好的支持日志控制和管理。
根据不同的日志系统，你可以按如下规则组织配置文件名，就能被正确加载：

|日志框架|配置文件名|
|--|--|
|Logback|logback-spring.xml, logback-spring.groovy, logback.xml, logback.groovy|
|Log4j|log4j-spring.properties, log4j-spring.xml, log4j.properties, log4j.xml|
|Log4j2|log4j2-spring.xml, log4j2.xml|
|JDK (Java Util Logging)|logging.properties|
+ Spring Boot官方推荐优先使用带有-spring的文件名作为你的日志配置（如使用logback-spring.xml，而不是logback.xml），命名为logback-spring.xml的日志配置文件，spring boot可以为它添加一些spring boot特有的配置项（下面会提到）。
上面是默认的命名规则，并且放在src/main/resources下面即可。如果你即想完全掌控日志配置，但又不想用logback.xml作为Logback配置的名字，可以在application.properties配置文件里面通过logging.config属性指定自定义的名字：`logging.config=classpath:logback.xml`
+ logback.xml 示例
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE xml>
<configuration  scan="true" scanPeriod="60 seconds" debug="false">
<contextName>logback</contextName>
<property name="log.path" value="log" />
<!--输出到控制台-->
<appender name="console" class="ch.qos.logback.core.ConsoleAppender">
<!-- <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
<level>ERROR</level>
</filter>-->
<encoder>
<pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
</encoder>
</appender>

<!--输出到文件-->
<appender name="file" class="ch.qos.logback.core.rolling.RollingFileAppender">
<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
<fileNamePattern>${log.path}/logback.%d{yyyy-MM-dd}.log</fileNamePattern>
</rollingPolicy>
<encoder>
<pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
</encoder>
</appender>

<root level="info">
<appender-ref ref="console" />
<appender-ref ref="file" />
</root>

<!-- logback为java中的包 -->
<logger name="com.mico.emptyspringboot.controller"/>
<!--logback.LogbackDemo：类的全路径 -->
<logger name="com.mico.emptyspringboot.controller.UserController" level="WARN" additivity="false">
<appender-ref ref="console"/>
</logger>
</configuration>
```
+ 根节点`<configuration>`包含的属性

+ scan：当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true。

+ scanPeriod：设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。

+ debug：当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。

+ 根节点`<configuration>`的子节点：`<configuration>`下面一共有2个属性，3个子节点，分别是：

+ 设置上下文名称`<contextName>`

每个logger都关联到logger上下文，默认上下文名称为“default”。但可以使用设置成其他名字，用于区分不同应用程序的记录。一旦设置，不能修改,可以通过%contextName来打印日志上下文名称。`<contextName>logback</contextName>`
+ 设置变量`<property>` 用来定义变量值的标签，有两个属性，name和value；其中name的值是变量的名称，value的值时变量定义的值。通过定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。`<property name="log.path" value="log" />`

+ 子节点`<appender>`

appender用来格式化日志输出节点，有俩个属性name和class，class用来指定哪种输出策略，常用就是控制台输出策略和文件输出策略。
控制台输出ConsoleAppender：
```
<!--输出到控制台-->
<appender name="console" class="ch.qos.logback.core.ConsoleAppender">
<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
<level>ERROR</level>
</filter>
<encoder>
<pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
</encoder>
</appender>
```
+ `<encoder>`表示对日志进行编码：
+ `%d{HH: mm:ss.SSS}`——日志输出时间。
+ `%thread`——输出日志的进程名字，这在Web应用以及异步任务处理中很有用。
+ `%-5level`——日志级别，并且使用5个字符靠左对齐。
+ `%logger{36}`——日志输出者的名字。
+ `%msg`——日志消息。
+ `%n`——平台的换行符。

ThresholdFilter为系统定义的拦截器，例如我们用ThresholdFilter来过滤掉ERROR级别以下的日志不输出到文件中。如果不用记得注释掉，不然你控制台会发现没日志
+ 输出到文件RollingFileAppender：
另一种常见的日志输出到文件，随着应用的运行时间越来越长，日志也会增长的越来越多，将他们输出到同一个文件并非一个好办法。RollingFileAppender用于切分文件日志：
```
<!--输出到文件-->
<appender name="file" class="ch.qos.logback.core.rolling.RollingFileAppender">
<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
<fileNamePattern>${log.path}/logback.%d{yyyy-MM-dd}.log</fileNamePattern>
<maxHistory>30</maxHistory>
<totalSizeCap>1GB</totalSizeCap>
</rollingPolicy>
<encoder>
<pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
</encoder>
</appender>
```

+ 其中重要的是rollingPolicy的定义：

*   `<fileNamePattern>${log.path}/logback.%d{yyyy-MM-dd}.log</fileNamePattern>`定义了日志的切分方式——把每一天的日志归档到一个文件中；

*   `<maxHistory>30</maxHistory>`表示只保留最近30天的日志，以防止日志填满整个磁盘空间。同理，可以使用`%d{yyyy-MM-dd_HH-mm}`来定义精确到分的日志切分方式；

*   `<totalSizeCap>1GB</totalSizeCap>`用来指定日志文件的上限大小，例如设置为1GB的话，那么到了这个值，就会删除旧的日志。

* logback 每天生成和大小生成冲突的问题可以看[这个解答](http://blog.csdn.net/wujianmin577/article/details/68922545)。

+ 子节点`<root>`
root节点是必选节点，用来指定最基础的日志输出级别，只有一个level属性，用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，不能设置为INHERITED或者同义词NULL。默认是DEBUG。可以包含零个或多个元素，标识这个appender将会添加到这个logger。
```
<root level="debug">
<appender-ref ref="console" />
<appender-ref ref="file" />
</root>
```
+ 子节点`<logger>`

`<logger>`用来设置某一个包或者具体的某一个类的日志打印级别、以及指定`<appender>`。`<logger>`仅有一个name属性，一个可选的level和一个可选的addtivity属性。

*   `name`：用来指定受此logger约束的某一个包或者具体的某一个类。

*   `level`：用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，还有一个特俗值INHERITED或者同义词NULL，代表强制执行上级的级别。如果未设置此属性，那么当前logger将会继承上级的级别。

*   `addtivity`：是否向上级logger传递打印信息。默认是true。

