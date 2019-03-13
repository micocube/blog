---
title: Java-Springboot-集成spring-security简单示例(Version-springboot-2-1-3-RELEASE
date: 2019-03-13 15:03:32
tags: SpringBoot全家桶
---
+ 使用Idea的Spring Initializr或者[SpringBoot官网下载quickstart](https://start.spring.io/)
+ 添加依赖
```
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-security</artifactId>
</dependency>
```
+ 新建控制器
```
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
public class UserController {
@GetMapping("/user")
public String getUsers() {
return "Hello Spring Security";
}
}
```
+ logback.xml
```
<?xml version="1.0" encoding="UTF-8"?>

<configuration>
<appender name="FILE" class="ch.qos.logback.core.FileAppender">
<file>/data/www/file/logs/springboot.log</file>

<encoder>
<pattern>%date %d{HH: mm:ss.SSS} %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
</encoder>
</appender>

<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
<encoder>
<pattern>%date %d{HH: mm:ss.SSS} %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
</encoder>
</appender>

<root level="debug">
<appender-ref ref="FILE" />
<appender-ref ref="STDOUT" />
</root>
</configuration>
```
+ application.properties
```
# Server Domain-Port
server.address=127.0.0.1
server.port=9090
```
+ 启动SpringBootApplication，springboot已经和spring-security集成了，如果直接访问`http://localhost:9090/user`会跳到登陆页面，这是spring-security自带的，但是我们并没有创建任何用户啊，spring-security有个默认的用户名`user`，密码在控制台![登陆页面](https://upload-images.jianshu.io/upload_images/5143802-623559782ac1ee40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
+ 默认密码在控制信息里,在控制台信息里搜索`Using generated`，当然你的程序生成的密码肯定和我的不一样
```
Using generated security password: 6ae529ee-2281-4b66-8f30-b1ba0e7fec97
```
+ 使用用户名和密码登陆后：
![正常访问](https://upload-images.jianshu.io/upload_images/5143802-e344f72a1bc82b00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

+ [源码](https://gitee.com/micocube/springboot-security/tree/master/1_quickstart)
