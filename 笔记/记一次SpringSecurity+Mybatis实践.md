---
title: "记一次SpringSecurity+Mybatis实践"
date: 2020-10-12T14:08:57+08:00
draft: false
tags: ["SpringBoot"]
categories: ["实践笔记"]
---

### 一、引入依赖

```xml
<dependency>
  <groupId>org.thymeleaf.extras</groupId>
  <artifactId>thymeleaf-extras-springsecurity4</artifactId>
  <version>3.0.4.RELEASE</version>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### 二、配置`WebSecurityConfigurerAdapter`

#### 使用内存用户储存且认证

```java
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true) // 开启方法级安全验证
public class SecurityConfig extends WebSecurityConfigurerAdapter {
	@Override
  protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()//表示下面是认证的配置
      .antMatchers("/login").permitAll()
      .antMatchers("/login/form").permitAll()
      .antMatchers("/css/**").permitAll()
      .antMatchers("/js/**").permitAll()
      .anyRequest()//任何请求
      .authenticated();//都需要身份认证
    http.formLogin()//表单认证
      .loginPage("/login")//使用本人制作的登录界面
      .usernameParameter("user")//username更改
      .loginProcessingUrl("/login/form")//表单提交的api
      .defaultSuccessUrl("/")//登录成功跳转页面
      .failureUrl("/login/error").permitAll();//登录错误跳转界面
    http.logout().logoutSuccessUrl("/login");//登出
    http.csrf().disable();//防止跨站攻击
  }
  @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()//使用内存用户储存
                //配置了一个user用户和一个admin用户
                .withUser("user").password("password").roles("USER").and()
                .withUser("admin").password("password").roles("USER", "ADMIN");
    }
}
```

### 三、由数据库认证登录

1. 在MySQL中创建用户表

```mysql
CREATE TABLE `admin` (
  `username` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci DEFAULT NULL,
  `password` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci DEFAULT NULL,
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `role` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
INSERT INTO `admin` VALUES ('admin', '123', 1, 'admin');
```

2. entity层创建实体

```java
@Data //使用lombok,简化get,set
public class UserInfo {
    private Integer id;
    private String username;
    private String password;
    private String role;
}
```

3. dao层创建数据存取对象

```java
@Mapper//mapper.xml分离
@Repository
public interface UserInfoDao {
    UserInfo getUserInfoByUsername(String username);
}

```

4. 创建dao对应的mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.llssit.ls.dao.UserInfoDao">

    <select id="getUserInfoByUsername" resultType="UserInfo">
        select * from admin where username = #{username}
    </select>

</mapper>
```

5. service层

`interface`

```java
public interface UserInfoSevice {
    UserInfo getUserInfoByUsername(String username);
}
```

`impl`

```java
@Service
public class UserInfoServiceImpl implements UserInfoSevice {
    @Autowired
    private UserInfoDao userInfoDao;

    @Override
    public UserInfo getUserInfoByUsername(String username) {
        return userInfoDao.getUserInfoByUsername(username);
    }
}
```

6. controller层或者其他运用

#### springsecurity数据库认证

首先需要重写接口`loadUserByUsername`

```java
@Component
public class MyUserDetailService implements UserDetailsService {
    @Autowired
    private UserInfoSevice userInfoService;
    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        // 通过用户名从数据库获取用户信息
        UserInfo userInfo = userInfoService.getUserInfoByUsername(s);
        if (userInfo == null) {
            throw new UsernameNotFoundException("用户不存在");
        }
        // 得到用户角色
        String role = userInfo.getRole();
        // 角色集合
        List<GrantedAuthority> authorities = new ArrayList<>();
        // 角色必须以`ROLE_`开头，数据库中没有，则在这里加
        authorities.add(new SimpleGrantedAuthority("ROLE_" + role));
        return new User(
                userInfo.getUsername(),
                // 因为数据库是明文，所以这里需加密密码
                new BCryptPasswordEncoder().encode(userInfo.getPassword()),
                authorities
        );
    }
}
```

然后创建`Security`的配置类`WebSecurityConfig`继承`WebSecurityConfigurerAdapter`，并重写`configure(auth)`方法

```java
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true) // 开启方法级安全验证
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private MyUserDetailService userDatailService;
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/login").permitAll()
                .antMatchers("/login/form").permitAll()
                .antMatchers("/css/**").permitAll()
                .antMatchers("/js/**").permitAll()
                .anyRequest()
                .authenticated();
        http.formLogin()
                .loginPage("/login")
                .usernameParameter("user")
                .loginProcessingUrl("/login/form")
                .defaultSuccessUrl("/")
                .failureUrl("/login/error").permitAll();
        http.logout().logoutSuccessUrl("/login");
        http.csrf().disable();

    }
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth
                // 从数据库读取的用户进行身份认证
                .userDetailsService(userDatailService)
                .passwordEncoder(new BCryptPasswordEncoder());
    }
}
```

#### 角色访问

1. `@EnableGlobalMethodSecurity(prePostEnabled = true) // 开启方法级安全验证`

2. 修改`Controller.java`类，增加方法的访问权限`@PreAuthorize("hasAnyRole('admin')")`



