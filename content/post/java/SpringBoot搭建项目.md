---
date: 2016-10-09T13:04:32+08:00
draft: false
tags: ["java"]
categories: ["技术相关"]
title: SpringBoot搭建项目
---

### Srping Boot简介
Spring Boot就是Spring一些库的集合，用来简化Spring应用的搭建及开发。

#### 特点

- 配置简单

- 内嵌tomcat，可以直接打成jar包运行

## 搭建demo

### 根据官方文档，快速搭建Spring Boot项目

http://projects.spring.io/spring-boot/

- 添加Maven依赖  

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.0.RELEASE</version>
</parent>
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

- 编写控制器  

```java
@RestController
public class HelloController {
    @RequestMapping(value = {"/hello", "/hi"}, method = RequestMethod.GET)
    public String say() {
        return "Hello";
    }
}
```

- Spring应用启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```

- 启动程序测试访问  
默认绑定8080端口，访问http://localhost:8080/hello

### 项目配置
使用yml配置文件

- application.yml

```yml
spring:
  profiles:
    active: dev
```

- application-dev.yml

```yml
server:
  port: 8082
  context-path: /dev
```

- application-prod.yml

```yml
server:
  port: 8080
  context-path: /
```

### 使用Spring-Data-Jpa访问Mysql

- 添加Maven依赖  

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
</dependency>
```

- 修改配置文件

```yml
spring:
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/test?characterEncoding=utf8&useSSL=true
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver
  jpa:
    hibernate:
      ddl-auto: update  #create:每次运行都会drop表再创建;update：不会删数据
    show-sql: true  #控制台会打印出sql语句
```

- 添加实体类（必须有无参构造函数）

```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
public class Test {
    @Id //表示主键
    @GeneratedValue //自动生成
    private Integer id;
    private String message;

    public Test() {
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }
}
```

- 创建接口

```java
public interface TestRepository extends JpaRepository<Test, Integer> {}
```

- 修改控制器

```java
public class HelloController {
    @Autowired
    private TestRepository testRepository;

    @RequestMapping(value = "test/{id}")
    public Test getOne(@PathVariable("id") Integer id) {
        return testRepository.findOne(id);
    }

    @RequestMapping(value = "add", method = RequestMethod.POST)
    public Test add(@RequestParam("message") String message) {
        Test test = new Test();
        test.setMessage(message);
        return testRepository.save(test);
    }
}
```

### 使用thymeleaf模板

- 添加Maven依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

- 修改配置文件

```yml
spring:
  thymeleaf:
    mode: HTML5
    encoding: UTF-8
    content-type: text/html
    cache: false    #开发时关闭缓存,不然没法看到实时页面
```

- 创建控制器

```java
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
public class IndexController {
    @RequestMapping(value = "index", method = RequestMethod.GET)
    public String index(Model model) {
        model.addAttribute("name", "Dear");
        return "index";
    }
}
```

- 创建视图

在resources/templates目录下创建index.html文件
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<!--/*@thymesVar id="name" type="java.lang.String"*/-->
<p th:text="'Hello！, ' + ${name} + '!'" >3333</p>
</body>
</html>
```

### 日志
spring-boot自动引入了slf4j和logback，使用时无需再引入包。  
```yml
logging:
  level:
    root: info
  path: ./logs/demo #将日志写到该目录下
```
日志文件达到10m后会自动分包。

### 热部署配置

```xml
<plugins>
    <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <!--配置热部署-->
        <dependencies>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>springloaded</artifactId>
                <version>1.2.7.RELEASE</version>
            </dependency>
        </dependencies>
    </plugin>
</plugins>
```
