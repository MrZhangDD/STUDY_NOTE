# 第一章、Spring Boot相关知识

##  一.springboot原理

**pom.xml**

- spring-boot-dependency 所有版本管理都在父任务中

### **1.1启动器**

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.6.RELEASE</version>
        <relativePath/>
</parent>
```

### **1.2主程序**

```java
@SpringBootApplication
public class Springboot01HelloworldApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot01HelloworldApplication.class, args);
    }
}
```

- ## 注解

  ```java
  @SpringBootApplication -- 作用：标注在某个类上说明这个类是SpringBoot的主配置类 ， SpringBoot就应该运行这个类的main方法来启动SpringBoot应用；
  	@Configuration -- spring配置类
  	@Component -- 说明这也是一个spring的组件
  
  @EnableAutoConfiguration  -- 自动配置
  	@AutoConfigurationPackage -- 自动配置包
  		@Import(AutoConfigurationPackages.Registrar.class) --导入选择器
  	@Import(AutoConfigurationImportSelector.class)  --  自动配置导入选择器
  
  ```

### 1.3.自动装配原理

1、SpringBoot启动会加载大量的自动配置类

2、看需要的功能有没有在SpringBoot默认写好的自动配置类当中；

3、再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件存在在其中，我们就不需要再手动配置了）

4、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可；

```xml
xxxxAutoConfigurartion：自动配置类；给容器中添加组件
xxxxProperties:封装配置文件中相关属性；
```

**springboot-web静态文件位置**

- resources下的resources>static>public
- resources下的templates只能通过controller来访问

## 二.springboot-security

Spring 是一个非常流行和成功的 Java 应用开发框架。Spring Security 基于 Spring 框架，提供了一套 Web 应用安全性的完整解决方案。一般来说，Web 应用的安全性包括用户认证（Authentication）和用户授权（Authorization）两个部分。用户认证指的是验证某个用户是否为系统中的合法主体，也就是说用户能否访问该系统。用户认证一般要求用户提供用户名和密码。系统通过校验用户名和密码来完成认证过程。用户授权指的是验证某个用户是否有权限执行某个操作。在一个系统中，不同用户所具有的权限是不同的。比如对一个文件来说，有的用户只能进行读取，而有的用户可以进行修改。一般来说，系统会为不同的用户分配不同的角色，而每个角色则对应一系列的权限。 

### 1.认识security

Spring Security 是针对Spring项目的安全框架，也是Spring Boot底层安全模块默认的技术选型，他可以实现强大的Web安全控制，对于安全控制，我们仅需要引入spring-boot-starter-security 模块，进行少量的配置，即可实现强大的安全管理！

记住几个类：

- WebSecurityConfigurerAdapter：自定义Security策略
- AuthenticationManagerBuilder：自定义认证策略
- @EnableWebSecurity：开启WebSecurity模式

Spring Security的两个主要目标是 “认证” 和 “授权”（访问控制）。

### 1.1“认证”（Authentication）

身份验证是关于验证您的凭据，如用户名/用户ID和密码，以验证您的身份。

身份验证通常通过用户名和密码完成，有时与身份验证因素结合使用。

###  1.2**“授权” （Authorization）**

授权发生在系统成功验证您的身份后，最终会授予您访问资源（如信息，文件，数据库，资金，位置，几乎任何内容）的完全权限。

这个概念是通用的，而不是只在Spring Security 中存在。

### 1.2.1.pom.xml引入security

```pom.xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### 1.2.2.编写security配置类

参考官网：https://spring.io/projects/spring-security  

https://docs.spring.io/spring-security/site/docs/5.3.0.RELEASE/reference/html5   #servlet-applications 8.16.4 

![1587614189561](C:\Users\DELL\AppData\Local\Temp\1587614189561.png)

#### 1.2.3.编写基础配置类**

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    /**
     * 配置角色
     * @param http
     * @throws Exception
     */
    @Override
    protected void configure(HttpSecurity http) throws Exception {}
}
```

#### 1.2.4.定制请求的授权规则 

```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .mvcMatchers("/").permitAll()
                .mvcMatchers("/level1/**").hasRole("vip1")
                .mvcMatchers("/level2/**").hasRole("vip2")
                .mvcMatchers("/level3/**").hasRole("vip3");
    }
```

### 2.开启登陆功能

```java
http.formLogin()；//没有权限跳转到登录页面
```

### 3.测试没有权限跳转到登录页面

![1587614443483](C:\Users\DELL\AppData\Local\Temp\1587614443483.png)

### 4.定制认证规则

重写configure(AuthenticationManagerBuilder auth)方法 

```java
/**  jdbcAuthentication()
     * 内存中存储用户信息，定制认证规则
     * @param auth
     * 密码需要加密否则会报错无法登陆
     */
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
                .withUser("zhang").password(new BCryptPasswordEncoder().encode("123456")).roles("vip3")
                .and()
                .withUser("root").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1","vip2","vip3");
    }
```

### 5.权限控制和注销 

#### 5.1开启自动注销功能

```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
       //*** 注销之后还可以指定跳转的页面(请求)
        http.logout().logoutSuccessUrl("/");
    }
```

#### 5.2 thymeleaf整合security

```html

<!-- https://mvnrepository.com/artifact/org.thymeleaf.extras/thymeleaf-extras-springsecurity4 -->
<dependency>
   <groupId>org.thymeleaf.extras</groupId>
   <artifactId>thymeleaf-extras-springsecurity5</artifactId>
   <version>3.0.4.RELEASE</version>
</dependency>
```

html页面引约束

```html
xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5"
或者
xmlns:sec="http://www.thymeleaf.org/extras/spring-security" //有提示

sec:authorize="!isAuthenticated() //判断用户是否有权限
sec:authentication="principal.username" //权限属性
```

#### 5.3注销404

如果注销404了，就是因为它默认防止csrf跨站请求伪造，因为会产生安全问题，我们可以将请求改为post表单提交，或者在spring security中关闭csrf功能；我们试试：在 配置中增加  http.csrf().disable(); 

```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
        //**
        //关闭csrf功能:跨站请求伪造,默认只能通过post方式提交logout请求
        http.csrf().disable();
       //*** 注销之后还可以指定跳转的页面(请求)
        http.logout().logoutSuccessUrl("/");
    }

```

#### 5.4 不同角色不同展示

```html
sec:authorize="hasRole('vip1')
```

#### 5.5打开‘记住我’功能

```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
       //*** 注销之后还可以指定跳转的页面(请求)
        http.logout().logoutSuccessUrl("/");
        //记住我
   	    http.rememberMe();
    }
//放入了session和cookie
```

### 6.定制登录页

#### 6.1点击登录跳转到登录页

```java
配置类中
@Override
    protected void configure(HttpSecurity http) throws Exception {
   	   //**
       //***没有权限跳转到登录页面  usernameParameter--form表单用户名
       //passwordParameter--密码 loginProcessingUrl--定制自己的登录页面
       http.formLogin().loginPage("/toLogin").
       usernameParameter("user").passwordParameter("pwd").
       loginProcessingUrl("/login"); 
    }
```

#### 6.2自定义记住我

```java
配置类中
@Override
    protected void configure(HttpSecurity http) throws Exception {
   	   //**
       //定制记住我的参数！
	http.rememberMe().rememberMeParameter("remember");
    }
```

### 7.完整配置类代码

```java
package com.zhang.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    /**
     * 配置角色
     * @param http
     * @throws Exception
     */
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .mvcMatchers("/").permitAll()
                .mvcMatchers("/level1/**").hasRole("vip1")
                .mvcMatchers("/level2/**").hasRole("vip2")
                .mvcMatchers("/level3/**").hasRole("vip3");
        http.formLogin()//没有权限跳转到登录页面
            .loginPage("/toLogin").usernameParameter("user")
            .passwordParameter("pwd")
            .loginProcessingUrl("/login"); //定制自己的登录页面

        //注销功能
        http.csrf().disable();//禁用csrf过滤功能
        http.logout().logoutSuccessUrl("/");
        //开启记住我功能.默认保存两周
        http.rememberMe().rememberMeParameter("remember");
    }

    /**
     * 内存中存储用户信息，定制认证规则
     * @param auth
     * @throws Exception
     */
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
            .passwordEncoder(new BCryptPasswordEncoder())
                .withUser("zhang")
            .password(new BCryptPasswordEncoder().encode("123456")).roles("vip3")
                .and()
                .withUser("root")
            	.password(new BCryptPasswordEncoder().encode("123456"))
            .roles("vip1","vip2","vip3");
    }
}
```

## 三.springboot-shiro

### **1.shiro简介**

![image-20200530153449404](https://gitee.com/ubfirst/Typora/raw/master/img/20200530153451.png) 

**Subject**：主体，可以看到主体可以是任何可以与应用交互的“用户”； 

**SecurityManager** ： 相 当 于 SpringMVC 中 的 DispatcherServlet 或 者 Struts2 中的FilterDispatcher；是 Shiro 的心脏；所有具体的交互都通过 SecurityManager 进行控制；它管理着所有 Subject、且负责进行认证和授权、及会话、缓存的管理。 

**Realm**：可以有 1 个或多个 Realm，可以认为是安全实体数据源，即用于获取安全实体的；可以是 JDBC 实现，也可以是 LDAP 实现，或者内存实现等等；由用户提供；注意：Shiro不知道你的用户/权限存储在哪及以何种格式存储；所以我们一般在应用中都需要实现自己的 Realm； realm中实现用户的认证和授权

### **2.pom.xml整合shiro**

```xml
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.4.1</version>
</dependency>
```

### 3.配置文件ShiroConfig.java

在配置中需要配置三个bean 

ShiroFilterFactoryBean

DefaultWebSecurityManager

UserRealm

```java
package com.zhang.config;
***
@Configuration
public class ShiroConfig {
    //shiroFillterFactoryBean
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("securityManager") DefaultSecurityManager securityManager){
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        /**
         * anon:无需认证
         * authc：必须认证
         * user：必须拥有记住我才能使用
         * perms：拥有对某个资源的权限才能使用
         * role：拥有某个角色才能访问
         */
        Map<String, String> filterChainDefinitionMap = new LinkedHashMap<>();
        filterChainDefinitionMap.put("/user/add","perms[user:add]");
        filterChainDefinitionMap.put("/user/update","perms[user:update]");
        //filterChainDefinitionMap.put("/user/*","authc"); //授权
        shiroFilterFactoryBean
        	.setFilterChainDefinitionMap(filterChainDefinitionMap);
        //设置登录页面
        shiroFilterFactoryBean.setLoginUrl("/toLogin");
        //未授权页面
        shiroFilterFactoryBean.setUnauthorizedUrl("/unlogin");
        return shiroFilterFactoryBean;
    }
----------------------------------------------------------------------------------
    //DefaultWebSecurityManager
    //@Qualifier("userRealm")  bean name注入
    @Bean("securityManager")
    public DefaultWebSecurityManager 
   		 getDefaultSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        DefaultWebSecurityManager securityManager = 
        	new DefaultWebSecurityManager();
        securityManager.setRealm(userRealm);
        return securityManager;
    }
    //realm
    //创建realm对象
    @Bean
    public UserRealm userRealm(){
        return new UserRealm();
    }
     //整合shiroDialect，thymeaf整合需要
    @Bean
    public ShiroDialect getShiroDialect(){
        return new ShiroDialect();
    }
}
```

### 4.配置Realm

AuthorizationInfo--授权

AuthenticationInfo--认证

```java
package com.zhang.config;
****
/**
 * 自定义的realm
 */
public class UserRealm extends AuthorizingRealm {
    //改用数据库查询用户信息
    @Autowired
    BooksService booksService;
    
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("进行授权==AuthorizationInfo");
        //为用户授权
        SimpleAuthorizationInfo authorizationInfo = new SimpleAuthorizationInfo();
        Subject subject = SecurityUtils.getSubject();
        Books books = (Books) subject.getPrincipal();
        authorizationInfo.addStringPermission(books.getPerms());
        return authorizationInfo;
    }
----------------------------------------------------------------------------------
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行认证==AuthenticationInfo");
        String name = "root";
        String pwd = "0000";
        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
        String username = token.getUsername();
        if(StringUtils.isEmpty(username)){
            return null;
        }
        Books books = booksService.quertByNm(token.getUsername());
        if(books == null){
            return null;
        }
        /*if(!StringUtils.equals(token.getUsername(),name)){
            return null;
        }*/
        //密码认证
        return new SimpleAuthenticationInfo(books,books.getBookCounts(),"");
    }
}
```

### 5.thymeleaf整合shiro

#### *5.1pom引入依赖*

```xml
<!-- https://mvnrepository.com/artifact/com.github.theborakompanioni/thymeleaf-extras-shiro -->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>
```

#### *5.2页面加入约束*

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org"
      xmlns:shiro="http://www.thymeleaf.org/thymeleaf-extras-shiro">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
    <h1 th:text="${msg}"></h1>
    <h1>首页</h1>
    <div shiro:hasPermission="user:add">
        <a th:href="@{/user/add}">add</a>
    </div>
    <div shiro:hasPermission="user:update">
        <a th:href="@{/user/update}">update</a>
    </div>
</body>
</html>
```

### 6.springboot整合shiro总结

6.1 首先引入依赖

6.2 需要手动配置shiroconfig

6.2.1在类中需要创建三个bean，层级由下往上依次注入

​		ShiroFilterFactoryBean

​		DefaultWebSecurityManager

​		UserRealm

6.3 配置Realm文件

​	Realm中对用户进行认证和授权操作，数据来源为数据库。subject为当前登录的用户，可以通过**Subject subject = SecurityUtils.getSubject()**可以获取到当前登录的用户信息。

## 四.Swagger

- 号称世界上最流行的API框架
- Restful Api 文档在线自动生成器 => **API 文档 与API 定义同步更新**
- 直接运行，在线测试API
- 支持多种语言 （如：Java，PHP等）
- 官网：https://swagger.io/

###  SpringBoot集成Swagger

**SpringBoot集成Swagger** => **springfox**，两个jar包

- **Springfox-swagger2**
- swagger-springmvc

### **使用Swagger**

要求：jdk 1.8 + 否则swagger2无法运行

步骤：

#### 1、新建一个SpringBoot-web项目

#### 2、添加Maven依赖

```xml
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger2</artifactId>
   <version>2.9.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger-ui</artifactId>
   <version>2.9.2</version>
</dependency>
```

#### 3.HelloController测试

#### 4.配置swaggerconfig

```java
package com.zhang.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import org.springframework.core.env.Environment;
import org.springframework.core.env.Profiles;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import java.util.ArrayList;

@Configuration
@EnableSwagger2 -- 开启swagger2自动配置
public class SwaggerConfig {

    //分组
    @Bean
    //配合swagger的docket的bean实例
    public Docket docket1(){
        //获取当前环境来决定是否激活swagger
        return new Docket(DocumentationType.SWAGGER_2).groupName("A");
    }
--------------------------------------------------------------------------
    @Bean
    //配合swagger的docket的bean实例
    public Docket docket2(){
        //获取当前环境来决定是否激活swagger
        return new Docket(DocumentationType.SWAGGER_2).groupName("B");
    }
--------------------------------------------------------------------------------
    @Bean
    //配合swagger的docket的bean实例
    public Docket docket(Environment environment){
        //获取当前环境来决定是否激活swagger
        Profiles profile = Profiles.of("dev","test");
        boolean b = environment.acceptsProfiles(profile);
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .enable(b) // 是否开启swagger
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.zhang.controller")) //扫描指定包
                .build().groupName("zhang");
    }
-----------------------------------------------------------------------------
    @Bean
    public ApiInfo apiInfo(){
        Contact contact = new Contact("学习", "http://192.168.16.1/login.asp", "1165889196@qq.com");
        return new ApiInfo(
                "Api 张",
                "学习日记",
                "1.0",
                "https://www.baidu.com/",
                contact,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList());
    }
}
```

#### 5.注意点

```java
return new Docket(DocumentationType.SWAGGER_2).groupName("A"); --分组使用
--扫描指定的包---
.select()
.apis(RequestHandlerSelectors.basePackage("com.zhang.controller")) //扫描指定包
.build();
--获取环境变量--
Environment environment
--判断是否为激活的环境--
Profiles profile = Profiles.of("dev","test");
boolean b = environment.acceptsProfiles(profile);
```

#### 6.swagger相关注解

```xml
@ApiModel("用户类") -- 用于类表示对类进行说明，用于参数用实体类接收 
@ApiModelProperty("密码") -- 注释实体类中相关字段名称
@ApiOperation("调用用户") -- 相关方法名称表示一个http请求的操作 
@ApiParam("用户参数") -- 方法接收的参数名称
```

#### 7.图示

![1587712931872](C:\Users\DELL\AppData\Local\Temp\1587712931872.png)

## 五.异步任务

### 1.启动类开启异步任务

```java
@SpringBootApplication
@EnableAsync
public class Springboot09AsyncApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot09AsyncApplication.class, args);
    }

}
```

### 2.需要异步执行的方法上加异步注解

```java
 @Async
    public void async(){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("执行线程----");
    }
```

## 六.邮件发送

### 1.springboot依赖

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
        </dependency>
```

### 2.配置邮件发送

```properties
spring.mail.password=KNIIYPVMAOOQYZFK
spring.mail.host=smtp.163.com
spring.mail.username=17633908117@163.com
```

### 3.测试邮件发送

```java
package com.zhang;
*****
@SpringBootTest
class Springboot10MailApplicationTests {

    @Autowired
    JavaMailSenderImpl javaMailSender;
    @Test
    void contextLoads() {
        //简单的邮件
        SimpleMailMessage simpleMailMessage = new SimpleMailMessage();
        simpleMailMessage.setSubject("springboot主题");
        simpleMailMessage.setText("好好学习，天天向上");
        simpleMailMessage.setFrom("17633908117@163.com");
        simpleMailMessage.setTo("17633908117@163.com");
        javaMailSender.send(simpleMailMessage);
    }
    @Test
    void testMail() throws MessagingException {
        //复杂的邮件
        MimeMessage mimeMessage = javaMailSender.createMimeMessage();
        MimeMessageHelper helper = new MimeMessageHelper(mimeMessage,true,"utf-8");
        helper.setSubject("springboot主题");
        helper.setText("<h1 style='color:red'>好好学习，天天向上</h1>",true);
        helper.addAttachment("1.jpg", 
        		new File("C:\\Users\\DELL\\Desktop\\1.jpg"));
        helper.setFrom("17633908117@163.com");
        helper.setTo("17633908117@163.com");
        javaMailSender.send(mimeMessage);
    }
}
```

## 七.定时任务

### 1.开启定时任务

```
@SpringBootApplication
@EnableScheduling -- 开启定时任务
public class Springboot11DingshiApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot11DingshiApplication.class, args);
    }

}
```

### 2.定时任务cron表达式

```java
package com.zhang.service;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Service;

@Service
public class ScheduledService {
    /**
     * 秒 分 时 日 月 周几
     */
    @Scheduled(cron = "0/5 * * * * ?")
    public void scheduled(){
        System.out.println("hello,定时执行了");
    }
}

```







# 第二章、常用的注解

## *@Bean("securityManager")* 

--标注为bean，及beanName

## *@Qualifier("userRealm")*

-- 添加指定的bean

## *@Mapper*

 -- mapper这个DAO交給Spring管理，编译时生成相应的实现类。此接口中不支持重载

## *@Repository*

-- 将dao层标识为spring bean，为了让 Spring 能够扫描类路径中的类并识别出此注解，需要在 XML/propertis/yaml 配置文件中启用Bean 的自动扫描功能

## *@Service*

-- 标注为service层

## *@Controller*

-- 标识为controller层

## *@RequestMapping(value="",method="")*

-- 请求映射

## *@PathVariable("id1")* 

-- 获取url路径中得参数 如 ‘@RequestMapping("/level{id1}")’

## *@RequestParam("id1")*

 --  获取url路径中指定名称后面得参数 'http://***/id1=12''

## *@Param("id1")*  

-- DAO层获取参数，用来转换映射到mapper.xml中

​		int deleteBookById(@Param("bookId") int id);

## *@Configuration*

--标注

# 第三章、常用的约束

## *1.MVC整合 application.xml*

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

<!--    关联所有的xml-->
    <import resource="classpath:spring-dao.xml"/>
    <import resource="classpath:spring-service.xml"/>
    <import resource="classpath:spring-mvc.xml"/>
</beans>
```

## *2.MVC整合 mybatis-config.xml*

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<!--    配置数据源-交给spring处理-->
    <typeAliases>
        <package name="com.zhang.pojo"/>
    </typeAliases>
    <mappers>
        <mapper class="com.zhang.dao.BookMapper"/>
    </mappers>
</configuration>
```

## *3.MVC整合 spring-mvc.xml*

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    
<!--    1.注解驱动-->
    <mvc:annotation-driven/>
<!--    2.静态资源过滤-->
    <mvc:default-servlet-handler/>
<!--    3.扫描包-->
    <context:component-scan base-package="com.zhang.controller"/>
<!--    4.视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```

## *4.MVC整合 spring-service.xml*

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

<!--    1.扫描service下的包-->
    <context:component-scan base-package="com.zhang.service"/>
<!--    2.将所有的业务类注入到spring-->
    <bean id="BookeServiceImpl" class="com.zhang.service.BookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
    </bean>

<!--    声明式事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
<!--       注入数据源-->
        <property name="dataSource" ref="dataSources"/>
    </bean>
</beans>
```

## *5.MVC整合 spring-dao.xml*

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

<!--    1.关联数据库配置文件-->
    <context:property-placeholder location="classpath:database.properties"/>
<!--    2.连接池
    dbcp:半自动需要手动连接
    c3p0：自动化加载配置文件，可以自动设置到对象中
    druid：
    hikari-->
    <bean id="dataSources" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        <!-- c3p0连接池的私有属性 -->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="10"/>
        <!-- 关闭连接后不自动commit -->
        <property name="autoCommitOnClose" value="false"/>
        <!-- 获取连接超时时间 -->
        <property name="checkoutTimeout" value="10000"/>
        <!-- 当获取连接失败重试次数 -->
        <property name="acquireRetryAttempts" value="2"/>
    </bean>
<!--    3.sqlSessionfactory-->
    <bean id="sqlSerssionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSources"/>
<!--        绑定mybatis配置文件-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
    </bean>

<!--    配置dao接口扫描包，动态实现dao接口注入到spring容器中-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
<!--        注入sqlSerssionFactory-->
        <property name="sqlSessionFactoryBeanName" value="sqlSerssionFactory"/>
<!--        要扫描dao包-->
        <property name="basePackage" value="com.zhang.dao"/>
    </bean>
</beans>
```

## *6.MVC整合properties文件*

```properties
jdbc.driver=com.mysql.jdbc.Driver
#如果使用的是MySQL8.0+需要加个时区设置 serverTimezone=UTC
jdbc.url=jdbc:mysql://localhost:3306/ssmbuild？useSSL=true&useUnicode=true&characterEncoding=utf8
jdbc.username=root
jdbc.password=root
```

## *7.MVC整合web.xml*

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    
<!--    dispatcherservlert-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
<!--    乱码过滤   -->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
<!--  session-->
    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>
</web-app>
```

## *8.MVC整合万能处理乱码*

```xml
<!--在springmvc配置文件中配置万能处理乱码-->
<mvc:annotation-driven>
   <mvc:message-converters register-defaults="true">
       <bean class="org.springframework.http.converter.StringHttpMessageConverter">
           <constructor-arg value="UTF-8"/>
       </bean>
       <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
           <property name="objectMapper">
               <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                   <property name="failOnEmptyBeans" value="false"/>
               </bean>
           </property>
       </bean>
   </mvc:message-converters>
</mvc:annotation-driven>
```



# 第四章、常用的jar包

```xml

<!-- 数据库连接驱动-->
<dependency>
     <groupId>mysql</groupId>
     <artifactId>mysql-connector-java</artifactId>
     <version>5.1.30</version>
</dependency>

<!--        c3p0连接池-->
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.5.2</version>
</dependency>
<!--       servlet依赖-->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
</dependency>
<!--       jsp依赖-->
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.2</version>
</dependency>
<!--       jstl依赖-->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
<!--      mybatis 依赖-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.3</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>2.0.3</version>
</dependency>
<!--      webmvc 依赖-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.5.RELEASE</version>
</dependency>
<!--      jdbc 依赖-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>
<!--      lombok 依赖 实例化对象-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.10</version>
</dependency>

<!--      thymeleaf整合springsecurity5-->
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-springsecurity5</artifactId>
</dependency>

 <!--	thymeleaf整合shiro	-->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>

<!--    静态资源导出问题-->
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
</build>

```

# 第五章、Web Service

## **1、什么是WebService？或者说webservice能给我们解决什么样的问题？** 

**WebService是一种跨编程语言和跨操作系统平台的远程调用技术。**

 比如银行端接口基本都是C语言编写，或者财政提供了接口，而银行想要远程调用，假若后端采用Java语言，那么如果想要调用这些接口，WebService就是很好的调用技术之一！ 

## **2、WebService的核心是什么？** 

`A.soap:`简单对象访问协议，它是轻型协议，用于分散的、分布式计算环境中交换信息。SOAP有助于以独立于平台的方式访问对象、服务和服务器。它借助于XML，提供了HTTP所需的扩展，即http+xml。

 `B.XML+XSD:`WebService平台中表示数据的格式是XML，XML解决了数据表示的问题，但它没有定义一套标准的数据类型，更没有说怎么去扩展这套数据类型，而XSD就是专门解决这个问题的一套标准。它定义了一套标准的数据类型，并给出了一种语言来扩展这套数据类型。

`C.wsdl:`基于XML用于描述Web Service及其函数、参数和返回值的文件。即web service的定义（描述）语言。 
 WebService服务器端通过一个WSDL文件来说明自己对外提供啥服务，该服务包括什么方法、参数、返回值等等。WSDL文件保存在Web服务器上，通过一个url地址就可以访问到它。
 客户端要调用一个WebService服务之前，首先要知道该服务的WSDL文件的地址。

 `WebService服务的WSDL文件地址可以通过两种方式来暴露：`
 1.注册到UDDI服务器，以便被人查找；
 2.直接告诉给客户端调用者。

`D.uddi:`它是目录服务，通过该服务可以注册和发布webservcie，以便第三方的调用者统一调用。

##  **3、Webservice的SEI指什么？**

 WebService服务器端用来处理请求的接口[WebService EndPoint Interface]

## **4、如何发布一个webservice？**

 A.定义SEI（接口） @webservice（类） @webMethod（暴露的方法）
 B.定义SEI的实现
 C.发布Endpoint.publish(url,new SEI的实现对象)

## **5、如何请求一个webservice？**

 A.根据wsdl文档生成客户端代码
 jdk wsimport -keep wsdl路径
 cxf wsdl2java wsdl路径

B.根据生成的代码调用webservice
 找到wsdl文档中service标签的name属性对应的类，找到这个port标签的name属性,调用该方法即可

## **6、WebService常用开发框架**

 Apache Axis1、Apache Axis2、Codehaus XFire、Apache CXF、Apache Wink、Jboss RESTEasy、sun JAX-WS（最简单、方便）、阿里巴巴 Dubbo等。

## 7．WSDL文档主要有那几部分组成，分别有什么作用？

一个WSDL文档的根元素是definitions元素，WSDL文档包含7个重要的元素：types, import, message, portType, operations, binding和service元素。

1、 definitions元素中一般包括若干个XML命名空间；

2、 Types元素用作一个容器，定义了自定义的特殊数据类型，在声明消息部分（有效负载）的时候，messages定义使用了types元素中定义的数据类型与元素；

3、 Import元素可以让当前的文档使用其他WSDL文档中指定命名空间中的定义；

4、 Message元素描述了Web服务的有效负载。相当于函数调用中的参数和返回值；

5、 PortType元素定义了Web服务的抽象接口，它可以由一个或者多个operation元素，每个operation元素定义了一个RPC样式或者文档样式的Web服务方法；

6、 Operation元素要用一个或者多个messages消息来定义它的输入、输出以及错误；

7、 Binding元素将一个抽象的portType映射到一组具体的协议（SOAP或者HTTP）、消息传递样式（RPC或者document）以及编码样式（literal或者SOAP encoding）；

8、 Service元素包含一个或者多个Port元素
每一个Port元素对应一个不同的Web服务，port将一个URL赋予一个特定的binding，通过location实现。
可以使两个或者多个port元素将不同的URL赋给相同的binding。

##  **8．SOAP是什么？**

  SOAP是simple object access protocal的缩写，即简单对象访问协议。 是基于XML和HTTP的一种通信协议。是webservice所使用的一种传输协议，webservice之所以能够做到跨语言和跨平台，主要是因为XML和HTTP都是独立于语言和平台的。Soap的消息分为请求消息和响应消息，一条SOAP消息就是一个普通的XML文档，包含下列元素：

1、 必需的 Envelope 元素，可把此XML文档标识为一条SOAP消息

2、 可选的 Header 元素，包含头部信息

3、 必需的 Body 元素，包含所有的调用和响应信息

4、 可选的 Fault 元素，提供有关在处理此消息所发生错误的信息

## **9.怎么理解UDDI？**

UDDI是Universal Description Discovery and Integration的缩写，即统一描述、发现和整合规范。用来注册和查找服务，把web services收集和存储起来，这样当别人访问这些信息的时候就从UDDI中查找，看有没有这个信息存在。

## 10.Webservice的SEI指什么？

WebService EndPoint Interface（webservice终端[Server端]接口）

就是 WebService服务器端用来处理请求的接口

 



 

 

 

 

 

 

 











# 第六章、RabbitMQ

## 一、什么是RabbitMQ?

采用AMQP高级消息队列协议的一种消息队列技术，最大的特点就是消费并不需要确保提供方存在，实现了服务之间的高度解耦。

## 二、为什么要使用RabbitMQ?

①在分布式系统下具备异步，削峰，负载均衡等一系列高级功能;

②拥有持久化的机制，进程消息，队列中的信息也可以保存下来。

③实现消费者和生产者之间的解耦。

④对于高并发场景下，利用消息队列可以使得同步访问变为串行访问达到一定量的限流，利于数据库的操作。

⑤可以使用消息队列达到异步下单的效果，排队中，后台进行逻辑下单。

## 三、RabbitMQ的使用场景有哪些?

①**跨系统的异步通信**，所有需要异步交互的地方都可以使用消息队列。就像我们除了打电话(同步)以外，还需要发短信，发电子邮件(异步)的通讯方式。

②**多个应用之间的耦合**，由于消息是平台无关和语言无关的，而且语义上也不再是函数调用，因此更适合作为多个应用之间的松耦合的接口。基于消息队列的耦合，不需要发送方和接收方同时在线。在企业应用集成(EAI)中，文件传输，共享数据库，消息队列，远程过程调用都可以作为集成的方法。

③**应用内的同步变异步**，比如订单处理，就可以由前端应用将订单信息放到队列，后端应用从队列里依次获得消息处理，高峰时的大量订单可以积压在队列里慢慢处理掉。由于同步通常意味着阻塞，而大量线程的阻塞会降低计算机的性能。

④**消息驱动的架构(EDA)**，系统分解为消息队列，和消息制造者和消息消费者，一个处理流程可以根据需要拆成多个阶段(Stage)，阶段之间用队列连接起来，前一个阶段处理的结果放入队列，后一个阶段从队列中获取消息继续处理。

⑤**应用需要更灵活的耦合方式**，如发布订阅，比如可以指定路由规则。

⑥**跨局域网**，甚至跨城市的通讯(CDN行业)，比如北京机房与广州机房的应用程序的通信。

## 四、RabbitMQ有哪些重要的角色?

RabbitMQ中重要的角色有：**生产者、消费者和代理**：

①生产者：消息的创建者，负责创建和推送数据到消息服务器;

②消费者：消息的接收方，用于处理数据和确认消息;

③代理：就是RabbitMQ本身，用于扮演“快递”的角色，本身不生产消息，只是扮演“快递”的角色。

## 五、如何确保消息正确地发送至RabbitMQ?如何确保消息接收方消费了消息?

### 1、发送方确认模式

①将信道设置成confirm模式(发送方确认模式)，则所有在信道上发布的消息都会被指派一个唯一的ID。

②一旦消息被投递到目的队列后，或者消息被写入磁盘后(可持久化的消息)，信道会发送一个确认给生产者(包含消息唯一 ID)。

③如果RabbitMQ发生内部错误从而导致消息丢失，会发送一条 nack(notacknowledged，未确认)消息。

④发送方确认模式是异步的，生产者应用程序在等待确认的同时，可以继续发送消息。当确认消息到达生产者应用程序，生产者应用程序的回调方法就会被触发来处理确认消息。

### 2、接收方确认机制

①消费者接收每一条消息后都必须进行确认(消息接收和消息确认是两个不同操作)。只有消费者确认了消息，RabbitMQ 才能安全地把消息从队列中删除。

②这里并没有用到超时机制，RabbitMQ仅通过Consumer的连接中断来确认是否需要重新发送消息。也就是说，只要连接不中断，RabbitMQ给了Consumer足够长的时间来处理消息。保证数据的最终一致性。

### 3、下面罗列几种特殊情况

①如果消费者接收到消息，在确认之前断开了连接或取消订阅，RabbitMQ会认为消息没有被分发，然后重新分发给下一个订阅的消费者。(可能存在消息重复消费的隐患，需要去重)

②如果消费者接收到消息却没有确认消息，连接也未断开，则RabbitMQ认为该消费者繁忙，将不会给该消费者分发更多的消息。

## 六、RabbitMQ怎么避免消息丢失?

①消息持久化;

②ACK确认机制;

③设置集群镜像模式;

④消息补偿机制。

## 七、要保证消息持久化成功的条件有哪些?

①声明队列必须设置持久化durable设置为 true。

②消息推送投递模式必须设置持久化，deliveryMode设置为2(持久)。

③消息已经到达持久化交换器。

④消息已经到达持久化队列。

以上四个条件都满足才能保证消息持久化成功。

## 八、RabbitMQ持久化有什么缺点?

持久化的缺地就是降低了服务器的吞吐量，因为使用的是磁盘而非内存存储，从而降低了吞吐量。可尽量使用ssd硬盘来缓解吞吐量的问题。

## 九、RabbitMQ 有几种广播类型?

三种广播模式：

①fanout：所有bind到此exchange的queue都可以接收消息(纯广播，绑定到RabbitMQ的接受者都能收到消息);

②direct：通过routingKey和exchange决定的那个唯一的queue可以接收消息;

③topic：所有符合routingKey(此时可以是一个表达式)的routingKey所bind的queue可以接收消息;

## 十、RabbitMQ中vhost的作用是什么?

vhost：每个 RabbitMQ 都能创建很多 vhost，我们称之为虚拟主机，每个虚拟主机其实都是 mini 版的RabbitMQ，它拥有自己的队列，交换器和绑定，拥有自己的权限机制。 

## 十一、RabbitMQ 有哪些重要的组件？

- ConnectionFactory（连接管理器）：应用程序与Rabbit之间建立连接的管理器，程序代码中使用。
- Channel（信道）：消息推送使用的通道。
- Exchange（交换器）：用于接受、分配消息。
- Queue（队列）：用于存储生产者的消息。
- RoutingKey（路由键）：用于把生成者的数据分配到交换器上。
- BindingKey（绑定键）：用于把交换器的消息绑定到队列上。

##  十二、RabbitMQ 怎么实现延迟消息队列？

延迟队列的实现有两种方式：

- 通过消息过期后进入死信交换器，再由交换器转发到延迟消费队列，实现延迟功能；
- 使用 RabbitMQ-delayed-message-exchange 插件实现延迟功能。

 十三、RabbitMQ 集群搭建需要注意哪些问题？

- 各节点之间使用“--link”连接，此属性不能忽略。
- 各节点使用的 erlang cookie 值必须相同，此值相当于“秘钥”的功能，用于各节点的认证。
- 整个集群中必须包含一个磁盘节点。

  RabbitMQ 每个节点是其他节点的完整拷贝吗？为什么？

不是，原因有以下两个：

- 存储空间的考虑：如果每个节点都拥有所有队列的完全拷贝，这样新增节点不但没有新增存储空间，反而增加了更多的冗余数据；
- 性能的考虑：如果每条消息都需要完整拷贝到每一个集群节点，那新增节点并没有提升处理消息的能力，最多是保持和单节点相同的性能甚至是更糟。

## 十四、 RabbitMQ 集群中唯一一个磁盘节点崩溃了会发生什么情况？

如果唯一磁盘的磁盘节点崩溃了，不能进行以下操作：

- 不能创建队列
- 不能创建交换器
- 不能创建绑定
- 不能添加用户
- 不能更改权限
- 不能添加和删除集群节点

唯一磁盘节点崩溃了，集群是可以保持运行的，但你不能更改任何东西。

## 十五、 RabbitMQ 对集群节点停止顺序有要求吗？

RabbitMQ 对集群的停止的顺序是有要求的，应该先关闭内存节点，最后再关闭磁盘节点。如果顺序恰好相反的话，可能会造成消息的丢失。

#  第七章、Zookeeper

## 一、 zookeeper 是什么？

zookeeper 是一个分布式的，开放源码的分布式应用程序协调服务，是 google chubby 的开源实现，是 hadoop 和 hbase 的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。

## 二、zookeeper 都有哪些功能？

- 集群管理：监控节点存活状态、运行请求等。
- 主节点选举：主节点挂掉了之后可以从备用的节点开始新一轮选主，主节点选举说的就是这个选举的过程，使用 zookeeper 可以协助完成这个过程。
- 分布式锁：zookeeper 提供两种锁：独占锁、共享锁。独占锁即一次只能有一个线程使用资源，共享锁是读锁共享，读写互斥，即可以有多线线程同时读同一个资源，如果要使用写锁也只能有一个线程使用。zookeeper可以对分布式锁进行控制。
- 命名服务：在分布式系统中，通过使用命名服务，客户端应用能够根据指定名字来获取资源或服务的地址，提供者等信息。

## 三、zookeeper 有几种部署模式？

zookeeper 有三种部署模式：

- 单机部署：一台集群上运行；
- 集群部署：多台集群运行；
- 伪集群部署：一台集群启动多个 zookeeper 实例运行。

## 四、 zookeeper 怎么保证主从节点的状态同步？

zookeeper 的核心是原子广播，这个机制保证了各个 server 之间的同步。实现这个机制的协议叫做 zab 协议。 zab 协议有两种模式，分别是恢复模式（选主）和广播模式（同步）。当服务启动或者在领导者崩溃后，zab 就进入了恢复模式，当领导者被选举出来，且大多数 server 完成了和 leader 的状态同步以后，恢复模式就结束了。状态同步保证了 leader 和 server 具有相同的系统状态。

## 五、集群中为什么要有主节点？

在分布式环境中，有些业务逻辑只需要集群中的某一台机器进行执行，其他的机器可以共享这个结果，这样可以大大减少重复计算，提高性能，所以就需要主节点。

## 六、集群中有 3 台服务器，其中一个节点宕机，这个时候 zookeeper 还可以使用吗？

可以继续使用，单数服务器只要没超过一半的服务器宕机就可以继续使用。

## 七、说一下 zookeeper 的通知机制？

客户端端会对某个 znode 建立一个 watcher 事件，当该 znode 发生变化时，这些客户端会收到 zookeeper 的通知，然后客户端可以根据 znode 变化来做出业务上的改变。

#  第八章、dubbo相关知识

1.分布式基础理论

分布式系统是若干独立计算机的集合，这些计算机对于用户来说就像单个相关系统 .

各模块之间需要交流关系则可以通过dubbo来管理

2.架构演变

![1588061199392](C:\Users\DELL\AppData\Local\Temp\1588061199392.png)

3.dubbo-RPC框架 -- 远程过程调用

# 第九章、代理模式

## 1.抽象图

![1588229557153](C:\Users\DELL\AppData\Local\Temp\1588229557153.png)

角色分析：

- 抽象角色：一般会使用接口或者抽象类来解决
- 真实角色：被代理的角色
- 代理角色：代理真实角色，代理真实角色后，一般会做些附属操作
- 客户：访问代理对象的人

代理模式好处：

- 可以使真实角色操作更加纯粹，不去关注一些公共业务
- 公共业务交给代理角色，实现业务的分工
- 公共代码发生扩展，方便集中管理

缺点：

- 一个真实角色产生一个代理角色，代码量翻倍

   

##  2.动态代理

- 动态代理和静态代理角色一样
- 动态代理的代理类是动态生成的，不是直接写好的
- 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  - 基于接口 -- jdk动态代理
  - 基于类 -- cglib
  - java字节码实现：javasist

 好处：

- 可以使真实角色操作更加纯粹，不去关注一些公共业务
- 公共业务交给代理角色，实现业务的分工
- 公共代码发生扩展，方便集中管理
- 一个动态代理类可以代理多个类只要是实现了同一个接口即可

 

#  第九章、Threadlocal

## 1.存储原理图

jdk1.8 之后涉及thread维护threadLocalMap-threadlocal:value

![1588301705602](C:\Users\DELL\AppData\Local\Temp\1588301705602.png)

1.每个map存储的entry数量变少
2.当thread销毁，threadlocal也销毁，减少内存使用

## 2.内存泄漏：

1.内存溢出：没有足够的内存提供申请者使用
2.内存泄漏：程序中已动态分配的内存由于某种原因无法释放，造成系统内存浪费，导致程序运行速度变慢或甚至系统崩溃，内存泄漏的堆积最终导致内存溢出

## 3.强引用和弱引用

弱引用：
	垃圾回收器一旦发现只具有弱引用的对象，不管当前内存空间是否足够都会回收他的内存

强引用：
	常见的普通对象引用，只强引用指向一个对象，就表明对象还活着，垃圾回收器不会回收这种对象
	

## 4.弱引用内存泄漏原因：

1.由于threadlocal和thread的声明周期一样长，没有手动删除对应的key就会导致内存泄漏

2.强引用也会导致内存泄漏，当引用threadlocal ref被回收之后，由于threadlocalmap和entry是强引用，在没有手动删除entry并且当前线程依然运行的情况下，垃圾回收器不会回收这个内存。

## 5.避免内存泄漏：

1.使用完threadlocal后remove对应的key
2.使用完threadlocal也结束当前线程 

如果忘记调用remove，threadlocalMap中的set/getEntry有对应的对key为null的判断，key为null则会设置value为null



# 第十章、mysql存储过程

## 1.使用说明

存储过程是数据库的一个重要对象，可以封装SQL语句集，可以用来完成一些比较复杂的业务逻辑，并且可以出参入参

## 2.创建语法

```sql
CREATE PROCEDURE 存储过程名（参数列表）
BEGIN
	存储过程体
END

注意：
1.参数列表包含三部分
参数模式  参数名  参数类型
举例：
IN stuname VARCHAR(20)
参数模式：
IN:改参数可以作为输入，需要调用方传入
OUT:改参数可以作为输出，可以作为返回值
INOUT:改参数既可以作为输入也可以作为输出，既需要传入值，又可以返回值
2.如果存储过程体只有一句话，begin end可以省略
存储过程体中的每条sql语句的结尾必须加分号
存储过程的结尾可以使用**DELIMITER**重新设置
语法：
DELIMITER 结束标记

```

## 3.调用语法

CALL 存储过程名（实参列表）

## 4.案例

### 4.1IN模式

```sql
DELIMITER $
CREATE PROCEDURE MYSQL2(IN username VARCHAR(10) charset 'utf8')
BEGIN 
	SELECT * from user where name =username;
END  $

CALL MYSQL2('张三')
```

```sql
#查询是否存在此用户
DELIMITER $
CREATE PROCEDURE my1(IN usernam VARCHAR(20) CHARSET 'utf8',in age VARCHAR(20) CHARSET 'utf8')
BEGIN
	DECLARE result INT DEFAULT 0; #声明一个变量并初始化
 select count(1) INTO result #赋值
 from `user` where `name` = usernam and user.age = age;
	SELECT IF(result >0,'成功','失败'); #使用
end $

CALL my1('张三','22')
```

### 4.2 OUT模式

```SQL
delimiter $
CREATE PROCEDURE my2(in name VARCHAR(12) CHARSET 'utf8',
OUT info VARCHAR(20) CHARSET 'utf8')
BEGIN
 select user.name from user where user.`name`=name;
END $
#set @info=1; #用户变量可声明可不声明
call my2('张三',@info);
SELECT @info;
```

```sql
#多个out
delimiter $$
CREATE PROCEDURE my3(in nm VARCHAR(20),OUT age INT(4),OUT id bigint(11))
BEGIN
	select user.age,user.id INTO age,id
	from user where `name` = nm;
END $$
#调用
CALL my3('马大哈',@age,@id);
#查询
select @age,@id
```

### 4.3 INOUT模式

```sql
delimiter $$
CREATE PROCEDURE my4(INOUT a int,INOUT b int)
BEGIN
	SET a=a*2;
	SET b=b*2;
END $$
#设置变量
set @m=10;
set @n=20;
call my4(@m,@n);
select @m,@n;
```

5.存储过程删除

```sql
语法：
DROP PROCEDURE my1;
```

6.查看存储过程的信息

```sql
SHOW CREATE PROCEDURE my1;
```

7.案例

```sql
#创建存储过程实现传入一个日期，格式化成xx年xx月xx日
delimiter %%
CREATE PROCEDURE my5(in date DATETIME,out	strDate VARCHAR(50))
BEGIN
 SELECT DATE_FORMAT(date,'%y年%m月%d日') INTO strDate;
END %%

CALL my5(now(),@strDate);
select @strDate;
```

# 第十一章、设计模式

## 1.单例模式

### 1.1饿汉式

```java
/**
 * 饿汉式单例
 * 不管用不用都加载对象，可能会浪费空间
 */
public class Hungry {

    //构造器私有
    private Hungry(){}
    private final static Hungry HUNGRY = new Hungry();
    public static Hungry getHungry(){
        return HUNGRY;
    }
}
```



 ### 1.2.懒汉式

 ```java
package com.zhang.single;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;

/**
 * 懒汉式单例
 * 双重检测锁模式，懒汉式单例，DCL懒汉式
 */
public class LazyMan04 {

    private static boolean zhang = false;
    private LazyMan04() {
        //防止反射获取对象
        synchronized (LazyMan04.class){
            if(zhang == false){
                zhang = true;
            } else {
                throw new RuntimeException("不要试图使用反射破坏异常");
            }
        }
    }
    //volatile保证可见性，可以防止指令重排序
    private volatile static LazyMan04 lazyMan04;

    public static LazyMan04 getInstance(){
        //双重检测锁模式，懒汉式单例，DCL懒汉式
        if(lazyMan04 == null){
            synchronized (LazyMan04.class){
                if(lazyMan04 == null){
                    lazyMan04 = new LazyMan04();
                    //不是原子性操作
                    /*
                    1.分配内存空间
                    2.执行构造方法，初始化对象
                    3.把这个对象只想这个空间
                    指令重排之后可能顺序错乱，会出现问题，导致先指向了地址，导致return的null
                     */
                }
            }
        }
        return lazyMan04;
    }

    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException, NoSuchFieldException {
        //LazyMan04 instance = LazyMan04.getInstance();
        //System.out.println(instance);
        //反射破坏单例
        /*Constructor constructor = LazyMan04.class.getDeclaredConstructor();
        constructor.setAccessible(true);
        LazyMan04 instance2 = (LazyMan04)constructor.newInstance();
        LazyMan04 instance3 = (LazyMan04)constructor.newInstance();
        //两个对象不是同一个
        System.out.println(instance3);
        System.out.println(instance2);
        */
        //反射破坏单例，获取属性并更改
        Constructor constructor = LazyMan04.class.getDeclaredConstructor();
        constructor.setAccessible(true);
        Field zhang = LazyMan04.class.getDeclaredField("zhang");
        zhang.setAccessible(true);
        LazyMan04 instance4 = (LazyMan04)constructor.newInstance();
        zhang.set(instance4,false);
        LazyMan04 instance5 = (LazyMan04)constructor.newInstance();
        System.out.println(instance4);
        System.out.println(instance5);
    }
}
 ```

静态内部类：

```java
package com.zhang.single;

/**
 * 静态内部类
 */
public class LazyMan05 {

    private LazyMan05(){}
    public static LazyMan05 getInstance(){
        return InnerClass.LAZY_MAN_05;
    }
    public static class InnerClass{
        private static final LazyMan05 LAZY_MAN_05 = new LazyMan05();
    }
}
```

最终解决方案：enum枚举不能被反射

```java
package com.zhang.single;

import java.lang.reflect.Constructor;

/**
 * 枚举
 */
public enum EnumSingle {
    INSTANCE;
    public EnumSingle getInstance(){
        return INSTANCE;
    }

    public static void main(String[] args) throws Exception {
        EnumSingle instance = EnumSingle.INSTANCE;
        Constructor constructor = EnumSingle.class.getDeclaredConstructor(String.class,int.class);
        constructor.setAccessible(true);
        EnumSingle instance2 = (EnumSingle)constructor.newInstance();

        System.out.println(instance);
        System.out.println(instance2);

    }
}
```

![1588513994465](C:\Users\DELL\AppData\Local\Temp\1588513994465.png)



# 第十二章、Sevlet技术

## 1.什么是servlet

​	1.servlet是javaEE规范之一，规范就是接口

​	2.Servlet是JavaWeb三大组件之一。三大组件分别是：Servlet程序，filter过滤器，listener监听器

​	3.servlet是运行在服务器上一个java小程序。可以接受客户端发送的请求并响应给客户端。

## 2.简单的servlet程序

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
<!--    servlet标签给tomcat配置servlet程序-->
    <servlet>
<!--        servlet-name 程序的别名-->
        <servlet-name>helloServlet</servlet-name>
        <servlet-class>com.zhang.servlet.HelloServlet</servlet-class>
    </servlet>
<!--    servlet-mapping  给servlet程序配置访问地址-->
    <servlet-mapping>
<!--        servlet-name 告诉服务请当前配置的地址给哪个servlet使用-->
        <servlet-name>helloServlet</servlet-name>

<!--      / 表示工程路径 http://localhost:8080/工程名称
        /hello  http://localhost:8080/工程名称/hello -->
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

```java
package com.zhang.servlet;

import javax.servlet.*;
import java.io.IOException;

public class HelloServlet implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {

    }
    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    /**
     * service专门处理请求和响应
     * @param servletRequest
     * @param servletResponse
     * @throws ServletException
     * @throws IOException
     */
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("HelloServlet");
    }
    @Override
    public String getServletInfo() {
        return null;
    }
    @Override
    public void destroy() {

    }
}
```

## 3.servlet生命周期

- a.执行servlet构造器

- b.执行init初始化方法

  ​	 第一次访问的时候会加载

- c.执行service方法

  ​	每次调用都会加载

- d.执行destroy销毁方法

  ​	停止web容器的时候

## 4.servletconfig类

**servletconfig是servlet程序在创建的时候就创建的**

作用：

- 获取servlet程序的别名
- 获取初始化参数
- 获取servletContext对象

**重写init方法里面一定要调用父类init方法**

```java
public class HelloServlet2 extends HttpServlet {
    @Override
    public void init(ServletConfig config) throws ServletException {
        super.init(config);
        System.out.println("调用初始化");
    }
}
```

## 5.ServletContext类

###      5.1 什么是ServletContext？

  - 一个web工程只有一个ServletContext对象实例

  - ServletContext是一个接口，表示servlet上下文

  - ServletContext是在web容器部署的时候创建，在web停止的时候销毁

  - ServletContext对象是一个域对象 -- 整个工程共享

    域对象？

    域对象是一个像map一样存储数据的对象，域是指存储数据的范围。

    ### 5.2 ServletContext类的四个作用

-  获取web.xml中配置的上下文参数 context-param

-  获取当前工程路径

-  获取工程部署后在服务器硬盘上的绝对路径

-  像map一样存储数据

## 6.HttpServletRequest类

### a）HttpServletRequest 类作用

​	每次请求进入tomcat服务器，tomcat服务器都会把请求过来的http协议信息解析封装到request对象中，传递到service方法。可以通过HttpServletRequest对象获取所有请求信息。

### b）html中base标签

base标签可以设置当前页面中所有相对路径工作时，参照哪个路径来进行跳转回去

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <base href="http://localhost:8080/hello/a/b.html">
</head>

<body>
    模拟跳转
    <a href="../../index.html">跳回首页</a>
<body>
http://localhost:8080/hello/index.html 
```

### c）Web中的相对路径和绝对路径

   相对路径：



|   .    |    表示当前目录     |
| :----: | :-----------------: |
|   ..   |   表示上一级目录    |
| 资源名 | 表示当前目录/资源名 |

### d）Web中的 /  的不同意义

​	在web中  /  表示一种绝对路径

​	/ 斜杠 如果被浏览器解析，得到的地址是：http://localhost:8080/

​	/ 斜杠 如果被浏览器解析，得到的地址是：http://localhost:8080/工程路径

​		1.<url-pattern>/servlet</url-pattern>

​		2.servletContext.getRealPath("/")

​		3.request.getRequestDispatcher("/")

​	特殊情况：response.sendRedirect("/");把斜杠发送到浏览器解析，

​			得到http://localhost:8080/

## 7.HttpServletResponse类

### a）HttpServletResponse类作用

​	每次请求进来Tomcat服务器都会创建一个response对象传递给servlet程序使用，HttpServletResponse表示所有响应的信息

### b）两个输出流的说明

| 字节流 | getOutputStream() | 常用语下载(传递二进制数据) |
| ------ | ----------------- | -------------------------- |
| 字符流 | getWriter()       | 常用语回传字符串           |

二者只能使用一个

### c）解决响应乱码

```java
package com.zhang.servlet;

import com.sun.deploy.net.HttpResponse;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class ContextServlet1 extends HttpServlet {

    //获取流对象之前才能使用转换编码
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //默认编码 ISO-8859-1
        System.out.println(resp.getCharacterEncoding());
        //设置服务器编码为UTF-8，浏览器编码不一致也会乱码
        //resp.setCharacterEncoding("UTF-8");
        //通过响应头设置浏览器编码也为UTF-8
        //resp.setHeader("Content-Type","text/html;charset=utf-8");
        resp.setContentType("text/html;charset=utf-8");
        PrintWriter writer = resp.getWriter();
        writer.write("好好学习");
    }
}
```

### d）重定向

- 浏览器地址栏发生变化
- 两次请求
- 不共享Request域
- 不能访问WEB-INF下的资源
- 可以访问工程外的资源

### e）请求转发

- 浏览器地址不变
- 一次请求
- 共享Request域
- 可以转发到WEB-INF下的资源
- 不可以访问工程外的资源

# 第十二章、jsp

## 1.什么是jsp，作用？

​	jsp-java server pages，java的服务器页面

​	jsp主要作用是代替Servlet程序回传html页面的数据

​	因为servlet回传html数据繁琐，维护麻烦。

## 2.jsp本质是什么

​	本质是一个servlet程序。

## 3.jsp头部的page指令

```xml
<%@ page contentType="text/html;charset=UTF-8" 
    isErrorPage="true" errorPage="/index.jsp" 
    autoFlush="true" buffer="8kb" language="java" %>
```

| language     | jsp翻译之后的语言文件                                        |
| ------------ | ------------------------------------------------------------ |
| contentType  | jsp返回的数据类型是什么                                      |
| pageEncoding | jsp页面本身的字符集                                          |
| import       | 导包                                                         |
| autoFlush    | 设置当out输出流缓冲区满了之后，是否自动刷新缓冲区，默认是true |
| buffer       | 设置out缓冲区大小，默认是8kb                                 |
| errorPage    | 出现错误之后重定向的页面                                     |
| isErrorPage  | 设置当前jsp页面是否为错误信息页面，默认是false，设置true可捕获异常信息 |
| session      | 访问当前jsp页面是否会创建HttpSession实例默认true             |
| extends      | 设置jsp翻译出来的java类继承谁                                |

4.jsp中常用的脚本

a) 声明脚本

```jsp
格式：<%! 声明java代码 %>
作用：可以给jsp翻译出来的java类定义属性和方法甚至是代码块，内部类等
```

b) 表达式脚本

```jsp
格式：<%= 表达式 %>
优点：
	1.所有的表达式脚本都会被翻译到_jspService() 方法中
	2.表达式脚本都会被翻译成out.print()输出到页面上
	3.由于表达式脚本翻译内容都在_jspService()方法中，_jspService()其中的对象都可以使用
	4.表达式脚本中表达式不能以分号结尾
```

c) 代码脚本

```java
代码脚本格式：<% java语句 %>
优点：
    1.代码脚本翻译之后都在_jspService()中
    2.脚本翻译内容都在_jspService()方法中，_jspService()其中的对象都可以使用
    3.多个代码脚本组合成一个完整的java语句
    4.代码脚本还可以和表达式脚本一起组合
```

4.jsp三种注释

a) html注释

​	out.write()输出注释

b) java注释

​	作为代码的注释

c) jsp注释

​	可以注掉jsp页面中所有代码

## 5.jsp九大内置对象

|   request   |      请求对象      |
| :---------: | :----------------: |
|  response   |      响应对象      |
| pageContext |   jsp上下文对象    |
|   session   |      会话对象      |
| application | ServletContext对象 |
|   config    | ServletConfig对象  |
|     out     |   jsp输出流对象    |
|    page     | 指向当前jsp的对象  |
|  exception  |      异常对象      |

6.jsp四大域对象

| pagecontext | （PageContext类）        | 当前jsp页面有效                                      |
| ----------- | ------------------------ | ---------------------------------------------------- |
| request     | （HttpServletRequest类） | 一次请求有效                                         |
| session     | （HttpSession类）        | 一次会话范围有效(打开浏览器到关闭)                   |
| application | （ServletContext类）     | 整个web工程范围都有效(只要web工程不停止，数据都存在) |

四个域都可以存储数据，优先级从小到大的范围顺序

pageContext 》request 》session 》 application

#  第十三章、git

## 版本控制

**1.本地版本控制 RCS**

记录文件每次的更新，可以对每个版本做一个快照，或是记录补丁文件，适合个人用，如RCS。

**2.集中式版本控制  SVN,CVS,VSS**

需要一个中央服务器，必须联网才能工作

**3.分布式版本控制 GIT**

每个人都有全部的代码，离线本地提交，联网远程提交

## GIT下载

[git官网] https://git-scm.com/，下载git对应操作系统的版本。

官网下载太慢，我们可以使用淘宝镜像下载：http://npm.taobao.org/mirrors/git-for-windows/

## GIT使用

**Git Bash：**Unix与Linux风格的命令行，使用最多，推荐最多

**Git CMD：**Windows风格的命令行

**Git GUI**：图形界面的Git，不建议初学者使用，尽量先熟悉常用命令

## 常用的Linux命令

1）、cd : 改变目录。

2）、cd . . 回退到上一个目录，直接cd进入默认目录

3）、pwd : 显示当前所在的目录路径。

4）、ls(ll):  都是列出当前目录中的所有文件，只不过ll(两个ll)列出的内容更为详细。

5）、touch : 新建一个文件 如 touch index.js 就会在当前目录下新建一个index.js文件。

6）、rm:  删除一个文件, rm index.js 就会把index.js文件删除。

7）、mkdir:  新建一个目录,就是新建一个文件夹。

8）、rm -r :  删除一个文件夹, rm -r src 删除src目录

```
rm -rf / 切勿在Linux中尝试！删除电脑中全部文件！
```

9）、mv 移动文件, mv index.html src index.html 是我们要移动的文件, src 是目标文件夹,当然, 这样写,必须保证文件和目标文件夹在同一目录下。

10）、reset 重新初始化终端/清屏。

11）、clear 清屏。

12）、history 查看命令历史。

13）、help 帮助。

14）、exit 退出。

15）、#表示注释

## GIT配置

1）、Git\etc\gitconfig  ：Git 安装目录下的 gitconfig   --system 系统级

2）、C:\Users\Administrator\ .gitconfig   只适用于当前登录用户的配置  --global 全局

- **查看配置 git config -l**

	[](D:\TyporaImgs\1589249567442.png)		

- **全局配置  git config --global -l**

![1589249722826](https://gitee.com/ubfirst/Typora/raw/master/img/20200530153635.png)

- **系统配置 git config --system -l**

![1589249705048](https://gitee.com/ubfirst/Typora/raw/master/img/20200530153640.png)

- **设置邮箱和用户名**

```text
 git config --global user.name 'zhang'  --用户名称
 git config --global user.email 'qq.com'  --用户邮箱
```

## Git基本理论（重要）

### 三个区域

Git本地有三个工作区域：工作目录（Working Directory）、暂存区(Stage/Index)、资源库(Repository或Git Directory)。如果在加上远程的git仓库(Remote Directory)就可以分为四个工作区域。文件在这四个区域之间的转换关系如下：

![image-20200512122029908](https://gitee.com/ubfirst/Typora/raw/master/img/20200530153647.png)

- Workspace：工作区，就是你平时存放项目代码的地方
- Index / Stage：暂存区，用于临时存放你的改动，事实上它只是一个文件，保存即将提交到文件列表信息
- Repository：仓库区（或本地仓库），就是安全存放数据的位置，这里面有你提交到所有版本的数据。其中HEAD指向最新放入仓库的版本
- Remote：远程仓库，托管代码的服务器，可以简单的认为是你项目组中的一台电脑用于远程数据交换

本地的三个区域确切的说应该是git仓库中HEAD指向的版本：

![img](https://gitee.com/ubfirst/Typora/raw/master/img/20200530153652.png)

- Directory：使用Git管理的一个目录，也就是一个仓库，包含我们的工作空间和Git的管理空间。
- WorkSpace：需要通过Git进行版本控制的目录和文件，这些目录和文件组成了工作空间。
- .git：存放Git管理信息的目录，初始化仓库的时候自动创建。
- Index/Stage：暂存区，或者叫待提交更新区，在提交进入repo之前，我们可以把所有的更新放在暂存区。
- Local Repo：本地仓库，一个存放在本地的版本库；HEAD会只是当前的开发分支（branch）。
- Stash：隐藏，是一个工作状态保存栈，用于保存/恢复WorkSpace中的临时状态。

## Git项目搭建

### 创建工作目录与常用指令

工作目录（WorkSpace)一般就是你希望Git帮助你管理的文件夹，可以是你项目的目录，也可以是一个空目录，建议不要有中文。

日常使用只要记住下图6个命令：

![img](https://gitee.com/ubfirst/Typora/raw/master/img/20200530153659.png)

### 本地仓库搭建

创建本地仓库的方法有两种：一种是创建全新的仓库，另一种是克隆远程仓库。

1、创建全新的仓库，需要用GIT管理的项目的根目录执行：

```
# 在当前目录新建一个Git代码库
$ git init
```

2、执行后可以看到，仅仅在项目目录多出了一个.git目录，关于版本等的所有信息都在这个目录里面。

### 克隆远程仓库

1、另一种方式是克隆远程目录，由于是将远程服务器上的仓库完全镜像一份至本地！

```
# 克隆一个项目和它的整个代码历史(版本信息)$ git clone [url]  
# https://gitee.com/kuangstudy/openclass.git
```

2、去 gitee 或者 github 上克隆一个测试！

## Git文件操作

### 文件的四种状态

- Untracked: 未跟踪, 此文件在文件夹中, 但并没有加入到git库, 不参与版本控制. 通过git add 状态变为Staged.
- Unmodify: 文件已经入库, 未修改, 即版本库中的文件快照内容与文件夹中完全一致. 这种类型的文件有两种去处, 如果它被修改, 而变为Modified. 如果使用git rm移出版本库, 则成为Untracked文件
- Modified: 文件已修改, 仅仅是修改, 并没有进行其他的操作. 这个文件也有两个去处, 通过git add可进入暂存staged状态, 使用git checkout 则丢弃修改过, 返回到unmodify状态, 这个git checkout即从库中取出文件, 覆盖当前修改 !
- Staged: 暂存状态. 执行git commit则将修改同步到库中, 这时库中的文件和本地文件又变为一致, 文件为Unmodify状态. 执行git reset HEAD filename取消暂存, 文件状态为Modified

### 查看文件状态

上面说文件有4种状态，通过如下命令可以查看到文件的状态：

```
#查看指定文件状态git status [filename]
#查看所有文件状态git status
# git add .                  添加所有文件到暂存区
# git commit -m "消息内容"    提交暂存区中的内容到本地仓库 -m 提交信息
```

### 忽略文件

有些时候我们不想把某些文件纳入版本控制中，比如数据库文件，临时文件，设计文件等

在主目录下建立".gitignore"文件，此文件有如下规则：

1. 忽略文件中的空行或以井号（#）开始的行将会被忽略。
2. 可以使用Linux通配符。例如：星号（*）代表任意多个字符，问号（？）代表一个字符，方括号（[abc]）代表可选字符范围，大括号（{string1,string2,...}）代表可选的字符串等。
3. 如果名称的最前面有一个感叹号（!），表示例外规则，将不被忽略。
4. 如果名称的最前面是一个路径分隔符（/），表示要忽略的文件在此目录下，而子目录中的文件不忽略。
5. 如果名称的最后面是一个路径分隔符（/），表示要忽略的是此目录下该名称的子目录，而非文件（默认文件或目录都忽略）。

```
#为注释*.txt        #忽略所有 .txt结尾的文件,这样的话上传就不会被选中！
!lib.txt           #但lib.txt除外
/temp        	   #仅忽略项目根目录下的TODO文件,不包括其它目录temp
build/             #忽略build/目录下的所有文件
doc/*.txt          #会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

## GIT分支

git分支中常用指令：

```
# 列出所有本地分支	
git branch
# 列出所有远程分支	
git branch -r
# 新建一个分支，但依然停留在当前分支			
git branch [branch-name]
# 新建一个分支，并切换到该分支			
git checkout -b [branch]
# 合并指定分支到当前分支$ 					
git merge [branch]
# 删除分支$ 							
git branch -d [branch-name]
# 删除远程分支$ 	
git push origin --delete [branch-name]$ git branch -dr [remote/branch]
# 查看本地仓库关联的远程仓库信息
git remote show origin
# 如果项目未关联过其他远程仓库执行  
git remote add origin https://git.com/*****/000.git
# 如果项目一关联过其他远程分支执行 
git remote set-url origin https://git.com/*****/000.git
# 关联本地分关联远程仓库分支执行 
git branch --set-upstream-to=origin/master(远程分支) master(本地分支)--allow-unrelated-histories


 
```

# 第十四章、mybatis

![](https://gitee.com/ubfirst/Typora/raw/master/img/20200530154243.png)

>  什么是MyBatis

- MyBatis 是一款优秀的**持久层框架**,一个半自动化的**ORM框架** 

- MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集的过程
- MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 实体类 【Plain Old Java Objects,普通的 Java对象】映射成数据库中的记录。
- MyBatis 本是apache的一个开源项目ibatis, 2010年这个项目由apache 迁移到了google code，并且改名为MyBatis 。
- 2013年11月迁移到**Github** .
- Mybatis官方文档 : http://www.mybatis.org/mybatis-3/zh/index.html
- GitHub : https://github.com/mybatis/mybatis-3

> MyBatis的优点

- 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件就可以了，易于学习，易于使用，通过文档和源代码，可以比较完全的掌握它的设计思路和实现。
- 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。sql写在xml里，便于统一管理和优化。通过sql语句可以满足操作数据库的所有需求。
- 解除sql与程序代码的耦合：通过提供DAO层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。
- 提供xml标签，支持编写动态sql。

## 1.Mybatis第一个程序

### 1.1创建maven项目

![1590592032760](E:/typoraImgs/1590592032760.png)

### 1.2导入相关依赖

```xml
<dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

### 1.3配置文件 mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf8&amp;serverTimezone=Asia/Shanghai"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
<!--    每一个mapper.xml文件都需要mybatis核心配置文件中注册-->
    <mappers>
        <mapper resource="com/zhang/dao/UserMapper.xml"/>
    </mappers>
</configuration>
```

### 1.4编写MyBatis工具类

```java
private static SqlSessionFactory sqlSessionFactory;
   static {
       try {
           String resource = "mybatis-config.xml";
           InputStream inputStream = Resources.getResourceAsStream(resource);
           sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
      } catch (IOException e) {
           e.printStackTrace();
      }
  }
   //获取SqlSession连接
   public static SqlSession getSession(){
       return sqlSessionFactory.openSession();
  }
```

## 2.核心配置解析

### 2.1核心配置文件

mybatis-config.xml

MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的
顶层结构如下：

```xml
▪ configuration（配置）
o  properties（属性）
o  settings（设置）
o  typeAliases（类型别名）
o  typeHandlers（类型处理器）
o  objectFactory（对象工厂）
o  plugins（插件）
o  environments（环境配置）
▪  environment（环境变量）
▪  transactionManager（事务管理器）
▪  dataSource（数据源）
o  databaseIdProvider（数据库厂商标识）
o  mappers（映射器）
```

### 2.2 环境配置（environments）

MyBatis 可以配置成适应多种环境，这种机制有助于将 SQL 映射应用于多种数据库之中， 现实情况下有多种理由需要这么做。例如，开发、测试和生产环境需要有不同的配置；或者想在具有相同 Schema 的多个生产数据库中使用相同的 SQL 映射。还有许多类似的使用场景。

**尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

Mybatis默认事务管理器JDBC，默认连接池POOLED

### 2.3、属性

通过配置文件实现引入配置文件

> 编写一个配置文件

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai
username=root
password=root
```

> 在核心配置文件中映射

```xml
<properties resource="db.properties"/>
```

- 可以引入外部指定文件
- 可以在其中增加一些属性
- 如果两个文件有同一个字段，有限使用外部配置文件

## 2.4.类型别名（typeAliases）

- 类型别名可为 Java 类型设置一个缩写名字
- 意在降低冗余的全限定类名书写

```xml
<select id="queryAll" resultType="User">
    select * from user
  </select>

<!--mybatis-config.xml中起别名-->
    <typeAliases>
        <typeAlias type="com.zhang.pojo.User" alias="User"></typeAlias>
    </typeAliases>
```

- 也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，比如，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。

```xml
<typeAliases>
        <typeAlias type="com.zhang.pojo“></typeAlias>
    </typeAliases>
```

- 实体类比较少使用第一种方式，实体类多使用第二种

第一种可以DIY别名，第二种可以通过注解

```java
@Alias("author")
public class Author {
    ...
}
```

## 2.5.设置

日志实现

|         |                                                       |       |
| ------- | ----------------------------------------------------- | ----- |
| logImpl | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。 | SLF4J |

SLF4J | LOG4J | LOG4J2 | JDK_LOGGING | COMMONS_LOGGING | STDOUT_LOGGING | NO_LOGGING

## 2.6.映射器（mappers）

方式一：

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```

方式二：使用class文件绑定注册

```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>

注：
- 接口和mapper配置文件必须同名
- 接口和mapper配置文件必须在同一个包下
```

方式三：使用扫描包进行注入绑定

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>

注：
- 接口和mapper配置文件必须同名
- 接口和mapper配置文件必须在同一个包下
```

## 2.7.作用域（Scope）和生命周期

![1590675099131](E:/typoraImgs/1590675099131.png)

不同作用域和生命周期类别是至关重要的，因为错误的使用会导致非常严重的并发问题。

**提示** **对象生命周期和依赖注入框架**

依赖注入框架可以创建线程安全的、基于事务的 SqlSession 和映射器，并将它们直接注入到你的 bean 中，因此可以直接忽略它们的生命周期。 如果对如何通过依赖注入框架使用 MyBatis 感兴趣，可以研究一下 MyBatis-Spring 或 MyBatis-Guice 两个子项目。

#### SqlSessionFactoryBuilder

- 这个类可以被实例化、使用和丢弃，一旦创建了 SqlSessionFactory，就不再需要它了。
- 局部变量

#### SqlSessionFactory

- 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。
- 最简单的就是使用**单例模式**或者**静态单例模式**。

#### SqlSession

- 连接到连接池的一个请求，非线程安全
- 作用域是请求或者方法作用域
- 用完之后关闭，避免资源被占用

![1590675855771](E:/typoraImgs/1590675855771.png)

每一个mapper代表一个业务。

## 3.ResultMap

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.zhang.dao.UserMapper">

    <resultMap id="userMap" type="User">
        <result column="id" property="id" />
        <result column="phone_num" property="pwd"/>
    </resultMap>

    <select id="queryAll" resultMap="userMap">
    select * from user
  </select>
</mapper>
```

## 4.日志

- LOG4J
- STDOUT_LOGGING

### 4.1标准日志实现

```xml
<!--日誌設置了，STDOUT_LOGGING 标准日志工厂实现-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
```

### 4.2 LOG4J

- Log4j是[Apache](https://baike.baidu.com/item/Apache/8512995)的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是[控制台](https://baike.baidu.com/item/控制台/2438626)、文件、[GUI](https://baike.baidu.com/item/GUI)组件

- 导入jar

```xml
<dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
```

- 配置文件编写

```xml
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/kuang.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

- 配置log4j的日志实现

```xml
<settings>
   <setting name="logImpl" value="LOG4J"/>
</settings>
```

## 5.分页

### 5.1使用mybatis分页

接口

```java
List<User> getUserByLimit(Map<String,Integer> map);
```

mapper.xml

```xml
 <!--resultType - 映射到实体类
        resultMap - 字段映射
    -->
    <select id="getUserByLimit" resultType="User" parameterType="map">
        select * from user limit #{startIndex},#{pageSize}
    </select>
```

测试

```java
@Test
    public void testLimit(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        Map<String, Integer> map = new HashMap<>();
        map.put("startIndex",0);
        map.put("pageSize",2);
        mapper.getUserByLimit(map);
        sqlSession.close();
    }
```

### 5.2.RowBounds分页

代码层面实现

接口

```java
List<User> getUserRowBounds();
```

mapper.xml

```xml
<select id="getUserRowBounds" resultType="com.zhang.pojo.User">
   select * from user
</select>
```

测试

```java
@Test
    public void testRowBound(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        //RowBound 实现分页
        RowBounds rowBound = new RowBounds(1,2);
        List<Object> list = sqlSession.selectList("com.zhang.dao.UserMapper.getUserRowBounds",null,rowBound);
        list.forEach(System.out::println);
        sqlSession.close();
    }
```

## 6.使用注解开发

### 6.1 面向接口开发

​		**解耦**

### 6.2使用注解开发

- sql 类型主要分成 :

- - @select ()
  - @update ()
  - @Insert ()
  - @delete ()

- **注意：**利用注解开发就不需要mapper.xml映射文件了 .

```java
1、我们在我们的接口中添加注解
//查询全部用户
@Select("select id,name,pwd password from user")
public List<User> getAllUser();

2、在mybatis的核心配置文件中注入
<!--使用class绑定接口-->
<mappers>
   <mapper class="com.zhang.dao.UserMapper"/>
</mappers>
```

## 7.lombok

### 7.1 引入依赖

```xml
		<dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.12</version>
        </dependency>
```

### 7.2 注解

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
```

### 7.3 优缺点

优点：

1. 能通过注解的形式自动生成构造器、getter/setter、equals、hashcode、toString等方法，提高了一定的开发效率
2. 让代码变得简洁，不用过多的去关注相应的方法
3. 属性做修改时，也简化了维护为这些属性所生成的getter/setter方法等

缺点：

1. 不支持多种参数构造器的重载
2. 虽然省去了手动创建getter/setter方法的麻烦，但大大降低了源代码的可读性和完整性，降低了阅读源代码的舒适度

# 第十五章、Stream API

Sream是什么？

数据渠道，用于操作数据源（集合，数组等）所生成的元素序列。

**集合讲的是数据，Stream讲的是计算**

> 注意

- Stream自己不会存储元素
- Stream不会改变源对象，相反会返回一个持有结果的新Stream
- Stream操作是延迟的，需要结果的时候才执行

> 操作步骤

- 1.创建Stream

  一个数据源（集合，数组）获得一个流

- 2.中间操作

  中间操作链，对数据源的数据进行处理

- 3.终止操作

  一旦执行终止操作，就执行中间操作链，并产生结果之后不会再被使用

> 代码实现

- 创建方式 

  - 1.通过集合

  ```java
   List<Person> list = PersonData.getPerson();
   //返回一个顺序流
   Stream<Person> stream = list.stream();
   //返回一个并行流
   Stream<Person> personStream = list.parallelStream();
  ```

  - 2.通过数组

  ```java
  int[] a = new int[]{1,2,3,4,5};
  //调用arrays类的static方法
  IntStream stream = Arrays.stream(a);
  
  Person za = new Person(1, "za");
  Person da = new Person(2, "da");
  Person[] people = {za, da};
  Stream<Person> stream1 = Arrays.stream(people);
  ```

  - 3.通过stream的of

  ```java
  Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5, 6);
  ```

- 中间操作

  - 1.筛选与切片

  ```java
  List<Person> list = PersonData.getPerson();
          //filter（） 过滤操作
          System.out.println("====filter====");
          list.stream().filter(e -> e.getId() > 1002)
                  .forEach(System.out::println);
  
          System.out.println("====limit====");
          //limit() 截断流 -- 使元素不超过给定数量
          list.stream().limit(3).forEach(System.out::println);
  
          //skip（） 跳过几个元素
          System.out.println("====skip====");
          list.stream().skip(3).forEach(System.out::println);
  
          //distinct () -- 筛选，通过hashcode()和equals()去重
          System.out.println("====distinct====");
          list.add(new Person(1001,"1001"));
          list.add(new Person(1001,"1001"));
          list.add(new Person(1001,"1001"));
          list.add(new Person(1001,"1001"));
  
          list.stream().distinct().forEach(System.out::println);
  ```

  - 2.映射

  ```java
   //map(Function f) --接受一个函数作为参数，将元素转换成其他形式
           // 或者提取信息，该函数会映射到每个元素上，并将其映射成一个新的元素
           List<String> list = Arrays.asList("aa", "bb", "cc");
           list.stream().map(str -> str.toUpperCase()).forEach(System.out:: println);
  
           List<Person> person = PersonData.getPerson();
           person.stream()
                   .map(Person::getId)
                   .filter(id -> id>1002)
                   .forEach(System.out::println);
           //flatMap(Function f) - 接受一个函数作为参数,将流中的每个值都换成另一个流，
           //然后把所有流连成一个流.相当于addAll(),打开集合套集合
  ```

  - 3.排序

  ```java
   //sorted -- 自然排序
          List<Integer> list = Arrays.asList(12, 15, 45, 65, 14, 54, 21);
          list.stream().sorted().forEach(System.out::println);
  
          //sorted(Comparator<? super P_OUT> comparator) 定制排序
          List<Person> personList = PersonData.getPerson();
          personList.stream()
                  .sorted((o1,o2) -> Integer.compare(o1.getId(),o2.getId()))
                  .forEach(System.out::println);
  ```

- stream终止操作

  - 1.匹配与查找

  ```java
   List<Person> personList = PersonData.getPerson();
          //allMatch 都通过才为true
          boolean b = personList.stream().allMatch(e -> e.getId() > 1001);
          System.out.println("====allMatch-->"+b);
  
          boolean b1 = personList.stream().anyMatch(e -> e.getId() > 1001);
          System.out.println("====anyMatch-->"+b1);
  
          boolean b2 = personList.stream().noneMatch(e -> e.getName().startsWith("张"));
          System.out.println("====noneMatch-->"+b1);
  
          Optional<Person> first = personList.stream().findFirst();
          System.out.println("====findFirst-->"+first);
  
          Optional<Person> any = personList.stream().findAny();
          System.out.println("====findAny-->"+any);
  
          //count个数
          long count = personList.stream().filter(e -> e.getId() > 1002).count();
          System.out.println("====count-->"+count);
  
          Optional<Integer> max = personList.stream().map(e -> e.getId()).max(Double::compare);
          System.out.println("====max-->"+max);
  
          Optional<Person> min = personList.stream().min((o1, o2) -> Double.compare(o1.getId(), o2.getId()));
          System.out.println("====min-->"+min);
  
          personList.stream().forEach(System.out::println);
  ```

  - 2.规约

  ```java
  //reduce()--可以流中元素反复结合起来，得到一个值，返回Optional<Person>
          List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
          //reduce(T identity--初始值, BinaryOperator<T> accumulator);
          Integer reduce = list.stream().reduce(0, Integer::sum);
          System.out.println(reduce);
  
          //Optional<T> reduce(BinaryOperator<T> accumulator);
          List<Person> personList = PersonData.getPerson();
          Stream<Integer> integerStream = personList.stream().map(Person::getId);
          System.out.println(integerStream.reduce(Integer::sum));
  ```

  - 3.收集

  ```java
  List<Person> person = PersonData.getPerson();
          List<Person> collect = person.stream().filter(e -> e.getId() > 1002).collect(Collectors.toList());
          collect.forEach(System.out::println);
  ```

