---
layout: post
title: spring-boot问题杂记
date: 2017-08-03
categories: blog
tags: [spring boot]
description: 在spring boot项目及与mybatis、jquery、thymeleaf等整合过程中遇到的问题。

---

# spring boot问题杂记

> 在spring boot项目及与mybatis、jquery、thymeleaf等整合过程中遇到的问题

1. 整合thymeleaf
    * 在pom中引入依赖：
    ``` xml
    <dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-thymeleaf</artifactId>
	</dependency>

    <dependency>
		<groupId>net.sourceforge.nekohtml</groupId>
		<artifactId>nekohtml</artifactId>
		<version>1.9.2
        
    ```
   * 在配置文件中进行配置：
    ``` 
    spring.thymeleaf.cache = false
    spring.thymeleaf.mode = LEGACYHTML5

    ```

    若不指定`LEGACYHTML5`模式的配置和引入`nekohtml`依赖，由于thymeleaf默认为严格HTML5形式，会无法识别类似`<input />`等自闭合标签。

1. 整合mybatis
    * pom中引入依赖：
    ``` xml
    <dependency>
		<groupId>org.mybatis.spring.boot</groupId>
		<artifactId>mybatis-spring-boot-starter</artifactId>
		<version>1.3.1</version>
	</dependency>

	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
	</dependency>

	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<optional>true</optional>
	</dependency>

    ```

    * Mapper开发中，若方法有多个参数，要加`@param`注解，否则只能识别第一个参数。例：
    ``` java
    UserEntity getUserByNameAndPass(@Param("userName")String userName,@Param("userPass")String userPass);

    ```

    * controller中定义`Mapper`对象进行操作时，要加`@Autowired`注解，否则会报空指针异常。例：
    ``` java
    @Autowired
    private UserMapper userMapper;

    ```

1. 使用Ajax
    * 引入jQuery
    ``` js
    <script type="text/javascript" th:src="@{/js/jquery-3.2.1.min.js}"></script>

    ```

    * Ajax以json格式向后台传值时，要把json对象的`data`部分转化为json串格式，否则后台无法接收。例：
    ``` js
    data : JSON.stringify({'userName' : userName, 'userPass' : userPass),
    
    ```















