---
title: Java-Maven+Idea-构建SpringBoot项目(光速入门)
date: 2019-03-13 15:03:32
tags: SpringBoot全家桶
---
+ springboot+jpa+druid构建Restful服务
+ [springboot官网](https://spring.io/projects/spring-boot)
+ maven 创建项目
```bash
mvn archetype:generate -DgroupId=com.mico.emptyspringboot -DartifactId=emptyspringboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractivMode=false
```
+ 当然你可以从springboot的[官网下载quickstart](https://start.spring.io/),也可以从intelij idea的File->New->Project->Spring Initializr
![Spring Initializr](https://upload-images.jianshu.io/upload_images/5143802-5fa56e8ca483b888.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![springboot-start](https://upload-images.jianshu.io/upload_images/5143802-34d56e255161106c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


+ pom.xml大概像这样:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<parent>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-parent</artifactId>
<version>2.1.2.RELEASE</version>
<relativePath/> <!-- lookup parent from repository -->
</parent>
<groupId>com.mico.domo.springboot</groupId>
<artifactId>demo</artifactId>
<version>0.0.1-SNAPSHOT</version>
<name>demo</name>
<description>Demo project for Spring Boot</description>

<properties>
<java.version>1.8</java.version>
</properties>

<dependencies>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
<groupId>mysql</groupId>
<artifactId>mysql-connector-java</artifactId>
<scope>runtime</scope>
</dependency>
<dependency>
<groupId>org.projectlombok</groupId>
<artifactId>lombok</artifactId>
<optional>true</optional>
</dependency>
<dependency>
<groupId>com.alibaba</groupId>
<artifactId>druid</artifactId>
<version>1.0.18</version>
</dependency>
<dependency>
<groupId>org.slf4j</groupId>
<artifactId>log4j-over-slf4j</artifactId>
</dependency>

<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-test</artifactId>
<scope>test</scope>
</dependency>
</dependencies>

<build>
<plugins>
<plugin>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
</plugins>
</build>

</project>

```
+ [本地已创建项目push到远程git](https://www.jianshu.com/p/ff080c9333c7)
+ springboot的配置文件application.properties
```
# Server Domain-Port
server.address=127.0.0.1
server.port=9090

#spring-datasource
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=UTF-8&useSSL=true
spring.datasource.username=root
spring.datasource.password=micocube
spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver
spring.datasource.initialSize=5
spring.datasource.minIdle=5
spring.datasource.maxActive=20
spring.datasource.maxWait=60000
spring.datasource.timeBetweenEvictionRunsMillis=60000
spring.datasource.minEvictableIdleTimeMillis=300000
spring.datasource.validationQuery=SELECT 1 FROM DUAL
spring.datasource.testWhileIdle=true
spring.datasource.testOnBorrow=false
spring.datasource.testOnReturn=false
spring.datasource.poolPreparedStatements=true
spring.datasource.maxPoolPreparedStatementPerConnectionSize=20
spring.datasource.filters=stat,wall,log4j
spring.datasource.connectionProperties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
spring.datasource.useGlobalDataSourceStat=true
#JPA Configuration:
spring.jpa.database=MYSQL
# Show or not log for each sql query
spring.jpa.show-sql=true
spring.jpa.generate-ddl=true
# Hibernate ddl auto (create, create-drop, update)
spring.jpa.hibernate.ddl-auto=update


#log
logging.config=classpath:logback.xml
LOG_PATH=./logs/coding.log



#encoding
spring.http.encoding.force=true
spring.http.encoding.charset=UTF-8
spring.http.encoding.enabled=true
server.tomcat.uri-encoding=UTF-8
```
+ springboot日志配置文件,logback.xml,[Springboot logback.xml配置文件详解](https://www.jianshu.com/p/0d04566fd236),
[Springboot 使用druid连接池抛ClassNotFoundException](https://www.jianshu.com/p/f04d04247f09)
```xml
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
+ springboot启动类Application.java
```java
package com.mico.domo.springboot.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

public static void main(String[] args) {
SpringApplication.run(DemoApplication.class, args);
}

}
```
+ 数据源配置
```java
package com.mico.emptyspringboot.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;

import javax.sql.DataSource;
import java.sql.SQLException;

/**
* DruidDBConfig类被@Configuration标注，用作配置信息；
* DataSource对象被@Bean声明，为Spring容器所管理，
* @Primary表示这里定义的DataSource将覆盖其他来源的DataSource。
*jdbc.url=${jdbc.url}
*最新的支持方式如下:
*jdbc.url=@jdbc.url@
*/
@Configuration
public class DruidDBConfig {
//  private Logger logger = LoggerFactory.getLogger(DruidDBConfig.class);

@Value("${spring.datasource.url}")
private String dbUrl;

@Value("${spring.datasource.username}")
private String username;

@Value("${spring.datasource.password}")
private String password;

@Value("${spring.datasource.driverClassName}")
private String driverClassName;

@Value("${spring.datasource.initialSize}")
private int initialSize;

@Value("${spring.datasource.minIdle}")
private int minIdle;

@Value("${spring.datasource.maxActive}")
private int maxActive;

@Value("${spring.datasource.maxWait}")
private int maxWait;

@Value("${spring.datasource.timeBetweenEvictionRunsMillis}")
private int timeBetweenEvictionRunsMillis;

@Value("${spring.datasource.minEvictableIdleTimeMillis}")
private int minEvictableIdleTimeMillis;

@Value("${spring.datasource.validationQuery}")
private String validationQuery;

@Value("${spring.datasource.testWhileIdle}")
private boolean testWhileIdle;

@Value("${spring.datasource.testOnBorrow}")
private boolean testOnBorrow;

@Value("${spring.datasource.testOnReturn}")
private boolean testOnReturn;

@Value("${spring.datasource.poolPreparedStatements}")
private boolean poolPreparedStatements;

@Value("${spring.datasource.maxPoolPreparedStatementPerConnectionSize}")
private int maxPoolPreparedStatementPerConnectionSize;

@Value("${spring.datasource.filters}")
private String filters;

@Value("{spring.datasource.connectionProperties}")
private String connectionProperties;

@Bean // 声明其为Bean实例
@Primary // 在同样的DataSource中，首先使用被标注的DataSource
public DataSource dataSource() {
DruidDataSource datasource = new DruidDataSource();

datasource.setUrl(this.dbUrl);
datasource.setUsername(username);
datasource.setPassword(password);
datasource.setDriverClassName(driverClassName);

// configuration
datasource.setInitialSize(initialSize);
datasource.setMinIdle(minIdle);
datasource.setMaxActive(maxActive);
datasource.setMaxWait(maxWait);
datasource.setTimeBetweenEvictionRunsMillis(timeBetweenEvictionRunsMillis);
datasource.setMinEvictableIdleTimeMillis(minEvictableIdleTimeMillis);
datasource.setValidationQuery(validationQuery);
datasource.setTestWhileIdle(testWhileIdle);
datasource.setTestOnBorrow(testOnBorrow);
datasource.setTestOnReturn(testOnReturn);
datasource.setPoolPreparedStatements(poolPreparedStatements);
datasource.setMaxPoolPreparedStatementPerConnectionSize(maxPoolPreparedStatementPerConnectionSize);
try {
datasource.setFilters(filters);
} catch (SQLException e) {

}
datasource.setConnectionProperties(connectionProperties);

return datasource;
}
}
```
+ App 配置类，WebAppConfigurer
```java
package com.mico.emptyspringboot.config;

import org.springframework.boot.web.servlet.MultipartConfigFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.http.converter.StringHttpMessageConverter;
import org.springframework.util.unit.DataSize;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import javax.servlet.MultipartConfigElement;
import java.nio.charset.Charset;

/**
* 访问uri:http://localhost:9090/swagger-ui.html
*/
@Configuration
public class WebAppConfigurer implements WebMvcConfigurer {


@Override
public void addViewControllers(ViewControllerRegistry registry) {
registry.addViewController("/sign_in").setViewName("thymeleaf/login");
registry.addViewController("/sign_up").setViewName("thymeleaf/registry");
/**
* 与上面的等效
@RequestMapping("/sign_in")
public String sign_in() {
return "thymeleaf/login";
}
@RequestMapping("/sign_up")
public String sign_up() {
return "thymeleaf/registry";
}
*/
}


@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
registry.addResourceHandler("/static/**").addResourceLocations("classpath:/static/");
}


@Bean
public MultipartConfigElement multipartConfigElement(){
MultipartConfigFactory factory = new MultipartConfigFactory();
factory.setMaxFileSize(DataSize.ofMegabytes(10));
factory.setMaxRequestSize(DataSize.ofMegabytes(10));
return factory.createMultipartConfig();
}

@Bean
public HttpMessageConverter<String> responseBodyConverter() {
StringHttpMessageConverter converter = new StringHttpMessageConverter(Charset.forName("UTF-8"));
return converter;
}
}
```
+ domain
```java
package com.mico.domo.springboot.demo.domain;

import lombok.Data;

import javax.persistence.*;

@Entity
@Table
@Data
public class Role {

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Integer id;
private String name;


public Role(){

}

public Role(String name) {
this.name = name;
}
}
```
```java
package com.mico.domo.springboot.demo.domain;

import lombok.Data;

import javax.persistence.*;
import java.util.List;

@Entity
@Table
@Data
public class User {

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Integer id;
private String username;
private String password;
@ManyToMany(cascade = {CascadeType.REFRESH},fetch = FetchType.EAGER)
private List<Role> roles;

public User() {
}

public User(String username, String password) {
this.username = username;
this.password = password;
}
}
```
+ DAO
```java
package com.mico.domo.springboot.demo.repository;

import com.mico.domo.springboot.demo.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;

public interface UserRepository extends JpaRepository<User, Long> {

User findByUsername(String name);

@Query(value = "FROM User u where u.username=:name")
User findUser(@Param("name") String name);

}
```
+ Service
```java
package com.mico.domo.springboot.demo.service;


import com.mico.domo.springboot.demo.domain.User;
import com.mico.domo.springboot.demo.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.List;
import java.util.Objects;

@Service
public class UserDetailsService {

@Autowired
private UserRepository userRepository;


public User loadUserByUsername(String s) {
return userRepository.findByUsername(s);
}
}

```
+ Controller
```java
package com.mico.domo.springboot.demo.controller;


import com.mico.domo.springboot.demo.domain.User;
import com.mico.domo.springboot.demo.service.UserDetailsService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import javax.servlet.http.HttpServletRequest;

@RestController
@RequestMapping("/user")
public class UserController {
@Autowired
private UserDetailsService userDetailsService;

@RequestMapping(value = "/{id}",method = RequestMethod.GET)
@ResponseBody
public User view(@PathVariable("id") Integer id) {
User user = new User();
user.setId(id);
user.setUsername("zhang");
return user;
}
@RequestMapping(value = "/login")
@ResponseBody
public User login(String userName, String password) {
User user = userDetailsService.loadUserByUsername(userName);
return user;
}
}
```
+ `spring.jpa.hibernate.ddl-auto=update`时jpa会自动创建数据库，插入数据
```sql
INSERT INTO test.role (id,name) VALUES (1,'User');
INSERT INTO test.user (id,username, password) VALUES (1,'root', '$2a$10$TeayMIrpuDwrpLHL5QsNpOcPeE/Kx3c4UYbi4NQzNkfKgf9YtL6F2');
INSERT INTO test.user_roles (user_id, roles_id) VALUES (1, 1);
```
+ Run DemoApplication.main会从9090端口启动服务
![最终效果](https://upload-images.jianshu.io/upload_images/5143802-88ed562bb5e11d1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
+ [项目源码](https://gitee.com/micocube/springboot-demo)


