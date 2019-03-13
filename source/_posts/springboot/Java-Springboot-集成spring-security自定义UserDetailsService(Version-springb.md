---
title: Java-Springboot-集成spring-security自定义UserDetailsService(Version-springb
date: 2019-03-13 15:03:32
tags: SpringBoot全家桶
---
+ 基于[Java Springboot 集成spring-security简单示例(Version:springboot-2.1.3.RELEASE)](https://www.jianshu.com/p/21b0e6e8639b)
+ 新建domain类，User和Role，用户和角色
```
package com.example.springboot.springbootsecurity.domain;

import com.fasterxml.jackson.annotation.JsonIgnore;
import lombok.Data;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.security.Timestamp;
import java.util.ArrayList;
import java.util.List;

@Data
public class User implements UserDetails {
private Long id;
private String username;
private String password;
private String nickname;
private boolean enabled = false;
private List<Role> roles;
private String email;
private String userface;
private Timestamp regTime;

@Override
@JsonIgnore
public boolean isAccountNonExpired() { // 帐户是否过期
return true;
}

@Override
@JsonIgnore
public boolean isAccountNonLocked() { // 帐户是否被冻结
return true;
}

// 帐户密码是否过期，一般有的密码要求性高的系统会使用到，比较每隔一段时间就要求用户重置密码
@Override
@JsonIgnore
public boolean isCredentialsNonExpired() {
return true;
}

@Override
public boolean isEnabled() {  // 帐号是否可用
return enabled;
}

public void setEnabled(boolean enabled) {
this.enabled = enabled;
}

@Override
@JsonIgnore
public List<GrantedAuthority> getAuthorities() {
List<GrantedAuthority> authorities = new ArrayList<>();
for (Role role : roles) {
authorities.add(new SimpleGrantedAuthority("ROLE_" + role.getName()));
}
return authorities;
}


public User(String username, String password, List<Role> roles) {
this.username = username;
this.password = password;
this.roles = roles;
}
}
```
```
package com.example.springboot.springbootsecurity.domain;

import lombok.Data;

@Data
public class Role {

private Integer id;
private String name;


public Role(){

}

public Role(String name) {
this.name = name;
}
}

```
+ 新建MyUserDetailsService类并实现UserDetailsService接口
```
package com.example.springboot.springbootsecurity.service;

import com.example.springboot.springbootsecurity.domain.Role;
import com.example.springboot.springbootsecurity.domain.User;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

import java.util.Arrays;

public class MyUserDetailsService implements UserDetailsService {

private Logger logger = LoggerFactory.getLogger(getClass());

@Override
public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
logger.info("用户的用户名: {}", username);
// TODO 根据用户名，查找到对应的密码，与权限

// 封装用户信息，并返回。参数分别是：用户名，密码，用户权限
String encode = new BCryptPasswordEncoder().encode("123456");
//123456   "$2a$10$rE5.RvkHaB06t.9GjGeaW.jNHysRQpBXObl3ZSahzBesfq7tAkX56"
User user = new User(username, encode,
Arrays.asList(new Role("admin")));
user.setEnabled(true);
return user;
}
}
```
+ 新建配置类WebSecurityConfig
```
package com.example.springboot.springbootsecurity.config;

import com.example.springboot.springbootsecurity.service.MyUserDetailsService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {


@Bean
UserDetailsService userDetails(){
return new MyUserDetailsService();
}


/**
* 没有passwordEncoder会抛java.lang.IllegalArgumentException:
*      There is no PasswordEncoder mapped for the id "null"
*/
@Bean
PasswordEncoder passwordEncoder(){
return new BCryptPasswordEncoder();
}


@Override
protected void configure(HttpSecurity http) throws Exception {
http.
formLogin()             // 定义当需要用户登录时候，转到的登录页面。
.and()
.authorizeRequests()    // 定义哪些URL需要被保护、哪些不需要被保护
.anyRequest()           // 任何请求,登录后可以访问
.authenticated();

}

}
```
+ 配置成功之后，在控制台查找不到任何`Using generated security password`即已生效，之后可以用任何用户名，密码只要123456就能登陆,详情查看MyUserDetailsService类
