---
layout: post
title: springboot学习笔记(一):基础程序和配置
---

#### 1 , springboot 介绍(来自百度百科)

##### 简介
编辑微服务是一个新兴的软件架构，就是把一个大型的单个应用程序和服务拆分为数十个的支持微服务。一个微服务的策略可以让工作变得更为简便，它可扩展单个组件而不是整个的应用程序堆栈，从而满足服务等级协议。

对于大型应用程序来说，增加更多的用户则意味着提供更大型的弹性计算云（EC2）实例规模，即便只是其中的一些功能扩大了规模亦是如此。其最终结果就是企业用户只需为支持超过微服务的那部分需求的EC2实例支付费用。

##### 微服务的优点
编辑微服务应用的一个最大的优点是，它们往往比传统的应用程序更有效地利用计算资源。这是因为它们通过扩展组件来处理功能瓶颈问题。这样一来，开发人员只需要为额外的组件部署计算资源，而不需要部署一个完整的应用程序的全新迭代。最终的结果是有更多的资源可以提供给其它任务。

微服务应用程序的另一个好处是，它们更快且更容易更新。当开发者对一个传统的单体应用程序进行变更时，他们必须做详细的QA测试，以确保变更不会影响其他特性或功能。但有了微服务，开发者可以更新应用程序的单个组件，而不会影响其他的部分。测试微服务应用程序仍然是必需的，但它更容易识别和隔离问题，从而加快开发速度并支持DevOps和持续应用程序开发。

第三个好处是，微服务架构有助于新兴的云服务，如事件驱动计算。类似AWS Lambda(AWS Lambda是一个用于部署代码、管理服务以及监控轻量级服务运行状态的细粒度方法)这样的功能让开发人员能够编写代码处于休眠状态，直到应用程序事件触发。事件处理时才需要使用计算资源，而企业只需要为每次事件，而不是固定数目的计算实例支付.

#### 2 , 最基本的springboot实践
- 创建标准的MavenWeb工程

- pom.xml依赖配置

```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.4.0.RELEASE</version>
</parent>

<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <java.version>1.7</java.version>
</properties>
    
<dependencies>
    <!-- web项目自动配置模块 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- 自动配置模板(包含spring-boot-starter-web依赖) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <!-- 日志配置 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-logging</artifactId>
    </dependency>
    </dependencies>

<build>
    <finalName>SpringBoot</finalName>
</build>
```

- Class

```
@SpringBootApplication
@RestController
public class SimpleExample {

    @RequestMapping("/")
    public String hello(){
        return "SpringBoot SimpleExample !";
    }

    public static void main(String[] args) {
        SpringApplication.run(Application.class,args);
    }
}
```

- 直接运行main方法
- 浏览器访问http://localhost:8080/ 即可显示下面的信息
```
SpringBoot SimpleExample ! 
```

#### 3, 默认配置和约定目录结构
- 默认web容器和端口
```
默认嵌入Tomcat , 端口号为8080
```

- spring-boot项目源码目录结构约定

```
Maven的资源文件目录：/src/java/resources
spring-boot项目静态文件目录：/src/java/resources/static
spring-boot项目模板文件目录：/src/java/resources/templates
```
spring-boot静态首页的支持,即index.html放在以下目录结构会直接映射到应用的根目录下
```
classpath:/META-INF/resources/index.html  
classpath:/resources/index.html  
classpath:/static/index.html  
calsspath:/public/index.html  
```
/src/java/resources/templates这个目录并不是首页文件的默认目录，所以我们需要手动将应用根路径映射到/src/java/resources/templates/index.html下.可以使用如下方式

```
@RequestMapping("/")  
    public String index(){  
        return "index";  
    }
```
- springboot默认配置文件(用于配置各类基本属性)
```
\src\main\resources\application.properties
```
参考文档 : [application.properties配置列表](http://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html)

下面是常用的一些配置项

```
# tomcat最大线程数，默认为200
server.tomcat.max-threads=800
# tomcat的URI编码
server.tomcat.uri-encoding=UTF-8
# 存放Tomcat的日志、Dump等文件的临时文件夹，默认为系统的tmp文件夹（如：C:\Users\Shanhy\AppData\Local\Temp）
server.tomcat.basedir=H:/springboot-tomcat-tmp
# 打开Tomcat的Access日志，并可以设置日志格式的方法：
#server.tomcat.access-log-enabled=true
#server.tomcat.access-log-pattern=
# accesslog目录，默认在basedir/logs
#server.tomcat.accesslog.directory=
# 日志文件目录
logging.path=H:/springboot-tomcat-tmp
# 日志文件名称，默认为spring.log
logging.file=myapp.log
```


#### 4 , 修改部分常用默认配置的方法以及页面展示数据基本流程

- 修改默认端口号,主要有两种方式
1. 通过配置文件修改

```
在application.properties文件中添加以下配置信息
server.port=8080
```
2. 实现EmbeddedServletContainerCustomizer接口

```
@SpringBootApplication
@RestController
public class Application implements EmbeddedServletContainerCustomizer{

    public static void main(String[] args) {
        SpringApplication.run(Application.class,args);
    }
    @Override
    public void customize(ConfigurableEmbeddedServletContainer configurableEmbeddedServletContainer) {
        configurableEmbeddedServletContainer.setPort(8081);
    }
}
```

- 关闭thymeleaf缓存

```
在application.properties文件中添加以下配置信息
spring.thymeleaf.cache: false  
server.tomcat.access_log_enabled: true  
server.tomcat.basedir: target/tomcat  
```
- 页面展示数据

[thymeleaf模板引擎官网](http://www.thymeleaf.org/)

[网上的参考信息](http://www.open-open.com/lib/view/open1383622135586.html)

例如:

```
@RequestMapping("/hello/{name}")
public String hello(@PathVariable("name") String name, Model model) {
    model.addAttribute("name", name);
    //默认会去\src\main\resources\templates目录下查找hello.html文件
    return "hello";
}
```
- 页面上展示

```
<!DOCTYPE HTML>
<!-- 需要引入thymeleaf命名空间 -->
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Getting Started: Serving Web Content</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
</head>
<body>
    <!-- 获取model中name的值 -->
    <p th:text="'Hello , ' + ${name} + '!'" />

</body>
</html>
```

### 5 , 部署和运行

- 继承SpringBootServletInitializer重写configure方法

```

@SpringBootApplication
@RestController
public class SimpleExample extends SpringBootServletInitializer{

    @RequestMapping("/")
    public String hello(){
        return "SpringBoot SimpleExample !";
    }
    
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return super.configure(builder);
    }

    public static void main(String[] args) {
        SpringApplication.run(Application.class,args);
    }
}
```

- 修改pom文件中jar 为 war


```
<!-- <packaging>jar</packaging> -->
<packaging>war</packaging>
```


- 修改pom，排除tomcat插件


```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-tomcat</artifactId>
            </exclusion>
        </exclusions>
</dependency>
```


















