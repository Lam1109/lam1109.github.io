---
date: 2020-07-22 12:27:40
layout: post
title: SpringMVC
subtitle: SpringMVC
description: SpringMVC.
image: /assets/img/post_img/springMVC.png
optimized_image: /assets/img/post_img/springMVC.png
category: Java
tags:
  - mvc
  - spring
author: Lam
paginate: true
---

# 第1章 Spring MVC是什么
## 1.1 概念
- MVC 是 **Model**、**View** 和 **Controller** 的缩写，分别代表 Web 应用程序中的 3 种职责。

> 模型：用于存储数据以及处理用户请求的业务逻辑。  
  视图：向控制器提交数据，显示模型中的数据。  
  控制器：根据视图提出的请求判断将请求和数据交给哪个模型处理，将处理后的有关结果交给哪个视图更新显示。
  
- 基于 Servlet 的 MVC 模式的具体实现如下：

> 模型：一个或多个 JavaBean 对象，用于存储数据（实体模型，由 JavaBean 类创建）和处理业务逻辑（业务模型，由一般的Java类创建）。  
  视图：一个或多个 JSP 页面，向控制器提交数据和为模型提供数据显示，JSP 页面主要使用 HTML 标记和 JavaBean 标记来显示数据。  
  控制器：一个或多个 Servlet 对象，根据视图提交的请求进行控制，即将请求转发给处理业务逻辑的 JavaBean，并将处理结果存放到实体模型 JavaBean 中，输出给视图显示。

- JSP中的 MVC 模式：

![image](/assets/img/post_img/MVCinJSP.png)

## 1.2 Spring MVC 工作流程
- Spring MVC 框架主要由 DispatcherServlet、处理器映射、控制器、视图解析器、视图组成，其工作原理如图所示。

![image](/assets/img/post_img/MVCWorkflow.png)

- Spring MVC 的工作流程如下：

> 1. 客户端请求提交到 DispatcherServlet。
> 2. 由 DispatcherServlet 控制器寻找一个或多个 HandlerMapping，找到处理请求的 Controller。
> 3. DispatcherServlet 将请求提交到 Controller。
> 4. Controller 调用业务逻辑处理后返回 ModelAndView。
> 5. DispatcherServlet 寻找一个或多个 ViewResolver 视图解析器，找到 ModelAndView 指定的视图。
> 6. 视图负责将结果显示到客户端。

## 1.3 Spring MVC 接口
- 在1.2节图中包含 4 个 Spring MVC 接口，即 **DispatcherServlet**、**HandlerMapping**、**Controller** 和 **ViewResolver**。

> 1. Spring MVC 所有的请求都经过 DispatcherServlet 来统一分发，在 DispatcherServlet 将请求分发给 Controller 之前需要借助 Spring MVC 提供的 HandlerMapping 定位到具体的 Controller。  
> 2. HandlerMapping 接口负责完成客户请求到 Controller 映射。  
> 3. Controller 接口将处理用户请求，这和 Java Servlet 扮演的角色是一致的。一旦 Controller 处理完用户请求，将返回 ModelAndView 对象给 DispatcherServlet 前端控制器，ModelAndView 中包含了模型（Model）和视图（View）。   
从宏观角度考虑，DispatcherServlet 是整个 Web 应用的控制器；从微观考虑，Controller 是单个 Http 请求处理过程中的控制器，而 ModelAndView 是 Http 请求过程中返回的模型（Model）和视图（View）。  
> 5. ViewResolver 接口（视图解析器）在 Web 应用中负责查找 View 对象，从而将相应结果渲染给客户。

--- 

# 第2章 第一个Spring MVC应用
## 2.1 详细步骤
### 2.1.1 创建 Web 应用并引入 JAR 包
- 在 IDEA 中创建一个名为 **springMVCDemo01** 的 Web 应用，在 **springMVCDemo01** 的 lib 目录中添加 Spring MVC 程序所需要的 JAR 包。

> spring-core-3.2.13.RELEASE.jar  
  spring-beans-3.2.13.RELEASE.jar  
  spring-context-3.2.13.RELEASE.jar  
  spring-expression-3.2.13.RELEASE.jar  
  commons-logging.1.2.jar
  spring-web-3.2.13.RELEASE.jar   
  spring-webmvc-3.2.13.RELEASE.jar  
  spring-aop-3.2.13.RELEASE.jar 

### 2.1.2 在 web.xml 文件中部署 DispatcherServlet

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">
    <display-name>springMVC</display-name>
    <!-- 部署 DispatcherServlet -->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 表示容器再启动时立即加载servlet -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <!-- 处理所有URL -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

- 上述 DispatcherServlet 的 servlet 对象 springmvc 初始化时将在应用程序的 WEB-INF 目录下查找一个配置文件，该配置文件的命名规则是“servletName-servlet.xml”，例如 springmvc-servlet.xml。

### 2.1.3 创建 Web 应用首页
- 在 **springMVCDemo01** 应用的 **WebContent** 目录下有个应用首页 **index.jsp**。index.jsp 的代码如下：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
         pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Insert title here</title>
</head>
<body>
未注册的用户，请<a href="${pageContext.request.contextPath }/register"> 注册</a>！
<br/>
已注册的用户，去<a href="${pageContext.request.contextPath }/login"> 登录</a>！
</body>
</html>
```

### 2.1.4 创建 Controller 类
- 在 src 目录下创建 **controller** 包，并在该包中创建 **RegisterController.java** 和 **LoginController** 两个传统风格的控制器类（实现了 Controller 接口），分别处理首页中“注册”和“登录”超链接的请求。

- LoginController.java

```java
package controller;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;
public class LoginController implements Controller {
    public ModelAndView handleRequest(HttpServletRequest arg0,
                                      HttpServletResponse arg1) throws Exception {
        return new ModelAndView("/WEB-INF/jsp/login.jsp");
    }
}
```

- RegisterController.java

```java 
package controller;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;
public class RegisterController implements Controller {
    public ModelAndView handleRequest(HttpServletRequest arg0,
                                      HttpServletResponse arg1) throws Exception {
        return new ModelAndView("/WEB-INF/jsp/register.jsp");
    }
}
```

### 2.1.5 创建 Spring MVC 配置文件并配置 Controller 映射信息

- 传统风格的控制器定义之后，需要在 Spring MVC 配置文件中部署它们（学习基于注解的控制器后不再需要部署控制器）。在 **WEB-INF** 目录下创建名为 **springmvc-servlet.xml** 的配置文件，具体代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!-- LoginController控制器类，映射到"/login" -->
    <bean name="/login" class="controller.LoginController"/>
    <!-- LoginController控制器类，映射到"/register" -->
    <bean name="/register" class="controller.RegisterController"/>
</beans>
```

### 2.1.5 创建应用的其他页面
- **RegisterController** 控制器处理成功后跳转到 **/WEB-INF/jsp** 下的 **register.jsp** 视图，**LoginController** 同理，因此在应用的 **/WEB-INF/jsp** 目录下应有 **register.jsp** 和 **login.jsp** 页面。

- login.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Login</title>
</head>
<body>
Login
</body>
</html>
```

- register.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Register</title>
</head>
<body>
Register
</body>
</html>
```

