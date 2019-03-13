---
title: Java-Springboot-使用druid连接池抛ClassNotFoundException
date: 2019-03-13 15:03:32
tags: SpringBoot全家桶
---
+ 抛错
```
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'dataSource' defined in class path resource [com/mico/domo/springboot/demo/config/DruidDBConfig.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [javax.sql.DataSource]: Factory method 'dataSource' threw exception; nested exception is java.lang.NoClassDefFoundError: org/apache/log4j/Logger
at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:627)
at org.springframework.beans.factory.support.ConstructorResolver.instantiateUsingFactoryMethod(ConstructorResolver.java:456)
at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.instantiateUsingFactoryMethod(AbstractAutowireCapableBeanFactory.java:1288)
at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1127)
at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:538)
at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:498)
at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:320)
at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:222)
at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:318)
at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:199)
at org.springframework.beans.factory.config.DependencyDescriptor.resolveCandidate(DependencyDescriptor.java:277)
at org.springframework.beans.factory.support.DefaultListableBeanFactory.doResolveDependency(DefaultListableBeanFactory.java:1244)
at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:1164)
at org.springframework.beans.factory.support.ConstructorResolver.resolveAutowiredArgument(ConstructorResolver.java:857)
at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:760)
... 28 common frames omitted
Caused by: org.springframework.beans.BeanInstantiationException: Failed to instantiate [javax.sql.DataSource]: Factory method 'dataSource' threw exception; nested exception is java.lang.NoClassDefFoundError: org/apache/log4j/Logger
at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:185)
at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:622)
... 42 common frames omitted
Caused by: java.lang.NoClassDefFoundError: org/apache/log4j/Logger
at com.alibaba.druid.filter.logging.Log4jFilter.<init>(Log4jFilter.java:26)
at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
at java.lang.Class.newInstance(Class.java:442)
at com.alibaba.druid.filter.FilterManager.loadFilter(FilterManager.java:114)
at com.alibaba.druid.pool.DruidAbstractDataSource.addFilters(DruidAbstractDataSource.java:1230)
at com.alibaba.druid.pool.DruidAbstractDataSource.setFilters(DruidAbstractDataSource.java:1219)
at com.mico.domo.springboot.demo.config.DruidDBConfig.dataSource(DruidDBConfig.java:102)
at com.mico.domo.springboot.demo.config.DruidDBConfig$$EnhancerBySpringCGLIB$$b8f4ceda.CGLIB$dataSource$0(<generated>)
at com.mico.domo.springboot.demo.config.DruidDBConfig$$EnhancerBySpringCGLIB$$b8f4ceda$$FastClassBySpringCGLIB$$caae8b62.invoke(<generated>)
at org.springframework.cglib.proxy.MethodProxy.invokeSuper(MethodProxy.java:244)
at org.springframework.context.annotation.ConfigurationClassEnhancer$BeanMethodInterceptor.intercept(ConfigurationClassEnhancer.java:363)
at com.mico.domo.springboot.demo.config.DruidDBConfig$$EnhancerBySpringCGLIB$$b8f4ceda.dataSource(<generated>)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
at java.lang.reflect.Method.invoke(Method.java:498)
at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:154)
... 43 common frames omitted
Caused by: java.lang.ClassNotFoundException: org.apache.log4j.Logger
at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:335)
at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
... 63 common frames omitted
```
+ 可以通过添加
```
<dependency>
<groupId>log4j</groupId>
<artifactId>log4j</artifactId>
<version>1.2.17</version>
</dependency>
```
+ 但是这样导致的问题是SpringBoot和Druid分别用自己的日志框架，SpringBoot用的是slf4j和logback。druid使用的日志框架是log4j,个人比较推荐的做法：引入log4j-over-slf4j包，作用是通过中间包来替换log4j日志框架，所有日志最终都统一到slf4j，并由logback实现。
```
<dependency>
<groupId>org.slf4j</groupId>
<artifactId>log4j-over-slf4j</artifactId>
<version>1.7.25</version>
</dependency>
```
+ [log4j-over-slf4j](https://www.slf4j.org/legacy.html):
To use log4j-over-slf4j in your own application, the first step is to locate and then to replace *log4j.jar* with *log4j-over-slf4j.jar*. Note that you still need an SLF4J binding and its dependencies for log4j-over-slf4j to work properly.

In most situations, replacing a jar file is all it takes in order to migrate from log4j to SLF4J.

Note that as a result of this migration, log4j configuration files will no longer be picked up. If you need to migrate your log4j.properties file to logback, the [log4j translator](http://logback.qos.ch/translator/) (用这个可以将log4j的配置文件转换成logback的配置文件)might be of help. For configuring logback, please refer to [its manual](http://logback.qos.ch/manual/index.html).
+ 附赠一个logback配置文件(logback.xml)
```
<?xml version="1.0" encoding="UTF-8"?>

<configuration>
<appender name="FILE" class="ch.qos.logback.core.FileAppender">
<file>/data/www/file/logs/springboot.log</file>

<encoder>
<pattern>%date %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
</encoder>
</appender>

<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
<encoder>
<pattern>%msg%n</pattern>
</encoder>
</appender>

<root level="debug">
<appender-ref ref="FILE" />
<appender-ref ref="STDOUT" />
</root>
</configuration>
```
