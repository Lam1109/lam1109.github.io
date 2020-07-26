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

> 1. 用户通过浏览器向服务器发送请求，请求会被Spring MVC的前端控制器 DispatcherServlet 所拦截。
> 2. DispatcherServlet 拦截到请求后，会调用 HandlerMapping 处理映射器。处理映射器根据请求URL找到具体的处理器，生成处理器对象及处理器拦截器（如果有则生成）一并返回给 DispatcherServlet。
> 3. DispatcherServlet 将请求提交到 Controller。
> 4. Controller 调用业务逻辑处理后返回一个 ModelAndView 对象，该对象中包含视图名或包含模型和视图名。
> 5. DispatcherServlet 根据 ModelAndView 对象寻找一个或多个合适的 ViewResolver 视图解析器。 ViewResolver 解析后，会向 DispatcherServlet 中返回具体的 View。
> 6. DispatcherServlet 对View进行渲染（即将模型数据填充志视图中），渲染结果会返回给浏览器显示。

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
**1) 创建 Web 项目并引入 JAR 包**
- 在 IDEA 中创建一个名为 **springMVCDemo01** 的 Web 应用，在 **springMVCDemo01** 的 lib 目录中添加 Spring MVC 程序所需要的 JAR 包。

> spring-core-3.2.13.RELEASE.jar  
  spring-beans-3.2.13.RELEASE.jar  
  spring-context-3.2.13.RELEASE.jar  
  spring-expression-3.2.13.RELEASE.jar  
  commons-logging.1.2.jar
  spring-web-3.2.13.RELEASE.jar   
  spring-webmvc-3.2.13.RELEASE.jar  
  spring-aop-3.2.13.RELEASE.jar 

**2) 在 web.xml 文件中部署 DispatcherServlet**

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

**3) 创建 Web 应用首页**
- 在 **springMVCDemo01** 应用的 **web** 目录下有个应用首页 **index.jsp**。index.jsp 的代码如下：

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

**4) 创建 Controller 类**
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

**5) 创建 Spring MVC 配置文件并配置 Controller 映射信息**

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

**6) 创建应用的其他页面**
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

**7) 发布并运行 Spring MVC 应用**
- 发布并运行 Spring MVC 应用到 **Tomcat**。
- 通过地址“http://localhost:8080/springMVCDemo01”首先访问 index.jsp 页面。
- 当用户单击“注册”超链接时，根据 springmvc-servlet.xml 文件中的映射将请求转发给 RegisterController 控制器处理，处理后跳转到 /WEB-INF/jsp 下的 register.jsp 视图。同理，当单击“登录”超链接时，控制器处理后转到 /WEB-INF/jsp下的login.jsp 视图。

## 2.2 视图解析器
- Spring 视图解析器是 Spring MVC 中的重要组成部分，用户可以在配置文件中定义 Spring MVC 的一个视图解析器（ViewResolver），示例代码如下：

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" >
    <!--前缀-->
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <!--后缀-->
    <property name="suffix" value=".jsp"/>
</bean>
```

# 第3章 核心类与常用注解
## 3.1 一个示例
## 3.1.1 Controller 注解类型
- 在 Spring MVC 中使用 org.springframework.stereotype.Controller 注解类型声明某类的实例是一个控制器。

**1) 创建项目**
- 创建一个 Spring MVC 应用 **springMVCDemo02** 来演示相关知识，springMVCDemo01 的 JAR 包、web.xml 与 springMVCDemo02 应用的 JAR 包、web.xml 完全一样。

**2) 创建控制类**
- 在 **springMVCDemo02** 应用的 src 目录下创建 **controller** 包，并在该包中创建 Controller 注解的控制器类 **IndexController**，示例代码如下：

```java
package controller;
import org.springframework.stereotype.Controller;
/**
* “@Controller”表示 IndexController 的实例是一个控制器
*
* @Controller相当于@Controller(@Controller) 或@Controller(value="@Controller")
*/
@Controller
public class IndexController {
    // 处理请求的方法
}
```

- 在 Spring MVC 中使用扫描机制找到应用中所有基于注解的控制器类，所以，为了让控制器类被 Spring MVC 框架扫描到，需要在配置文件中声明 spring-context，并使用 <context：component-scan/> 元素指定控制器类的基本包（请确保所有控制器类都在基本包及其子包下）。

**3) 创建配置文件**
- 在 **springMVCDemo02** 应用的 **/WEB-INF/** 目录下创建配置文件 **springmvc-servlet.xml**，示例代码如下：

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
    <!-- 使用扫描机制扫描控制器类，控制器类都在controller包及其子包下 -->
    <context:component-scan base-package="controller" />
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

### 3.1.2 RequestMapping 注解类型
#### 3.1.2.1 方法级别注解 
- 修改 **IndexController** 类。

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
/**
 * “@Controller”表示 IndexController 的实例是一个控制器
 *
 * @Controller相当于@Controller(@Controller) 或@Controller(value="@Controller")
 */
@Controller
public class IndexController {
    @RequestMapping(value = "/index/login")
    public String login() {
        /**
         * login代表逻辑视图名称，需要根据Spring MVC配置
         * 文件中internalResourceViewResolver的前缀和后缀找到对应的物理视图
         */
        return "login";
    }
    @RequestMapping(value = "/index/register")
    public String register() {
        return "register";
    }
}
```
- 用户可以使用如下 URL 访问 login 方法（请求处理方法），在访问 login 方法之前需要事先在 **/WEB-INF/jsp/** 目录下创建 **login.jsp**。

> http://localhost:8080/springMVCDemo02/index/login

#### 3.1.2.2 类级别注解
- 修改 **IndexController** 类。

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@RequestMapping("/index")
public class IndexController {
    @RequestMapping("/login")
    public String login() {
        return "login";
    }
    @RequestMapping("/register")
    public String register() {
        return "register";
    }
}
```

- 用户可以使用如下 URL 访问 login 方法。

> http://localhost:8080/springMVCDemo02/index/login

- 为了方便维护程序，建议开发者采用类级别注解，将相关处理放在同一个控制器类中。例如，对商品的增、删、改、查处理方法都可以放在同一个控制类中。

## 3.2 编写请求处理方法
- 在控制类中每个请求处理方法可以有多个不同类型的参数，以及一个多种类型的返回结果。

### 3.2.1 请求处理方法中常出现的参数类型
- 如果需要在请求处理方法中使用 Servlet API 类型，那么可以将这些类型作为请求处理方法的参数类型。Servlet API 参数类型的示例代码如下：

```java
package controller;
import javax.servlet.http.HttpSession;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@RequestMapping("/index")
public class IndexController {
    @RequestMapping("/login")
    public String login(HttpSession session,HttpServletRequest request) {
        /*在对应的视图（login.jsp）中可以使用EL表达式${skey}取出session中的值*/
        session.setAttribute("skey", "session范围的值");
        session.setAttribute("rkey", "request范围的值");
        return "login";
    }
}
```

- 除了 Servlet API 参数类型以外，还有输入输出流、表单实体类、注解类型、与 Spring 框架相关的类型等。其中特别重要的类型是 **org.springframework.ui.Model** 类型，该类型是一个包含 Map 的 Spring 框架类型。在每次调用请求处理方法时 Spring MVC 都将创建 **org.springframework.ui.Model** 对象。Model 参数类型的示例代码如下：

```java
package controller;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@RequestMapping("/index")
public class IndexController {
    @RequestMapping("/register")
    public String register(Model model) {
        /*在对应的视图（register.jsp）中可以使用EL表达式${success}取出model中的值*/
        model.addAttribute("success", "注册成功");
        return "register";
    }
}
```

# 第4章 Spring MVC 获取参数
## 4.1 通过实体 Bean 接收请求参数
- 通过一个实体 Bean 来接收请求参数，适用于 get 和 post 提交请求方式。
- 需要注意的是，Bean 的属性名称必须与请求参数名称相同。

**1) 创建首页面**
- 在 **springMVCDemo02** 应用的 **web** 目录下修改 **index.jsp** 页面，代码如下：

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
没注册的用户，请<a href="${pageContext.request.contextPath }/index/register"> 注册</a>！
<br/>
已注册的用户，去<a href="${pageContext.request.contextPath }/index/login"> 登录</a>！
</body>
</html>
```

**2) 完善配置文件**
- 完善配置文件 **springmvc-servlet.xml**，代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:p="http://www.springframework.org/schema/p" xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!-- 使用扫描机制扫描控制器类，控制器类都在controller包及其子包下 -->
    <context:component-scan base-package="controller" />
    <mvc:annotation-driven />
    <!-- annotation-driven用于简化开发的配置，注解DefaultAnnotationHandlerMapping和AnnotationMethodHandlerAdapter -->
    <!-- 使用resources过滤掉不需要dispatcherservlet的资源（即静态资源，例如css、js、html、images）。
        在使用resources时必须使用annotation-driven，否则resources元素会阻止任意控制器被调用 -->
    <!-- 允许css目录下的所有文件可见 -->
    <mvc:resources location="/css/" mapping="/css/**" />
    <!-- 允许html目录下的所有文件可见 -->
    <mvc:resources location="/html/" mapping="/html/**" />
    <!-- 允许images目录下的所有文件可见 -->
    <mvc:resources location="/images/" mapping="/images/**" />
    <!-- 完成视图的对应 -->
    <!-- 对转向页面的路径解析。prefix：前缀， suffix：后缀 -->
    <bean
            class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

**3) 创建 POJO 实体类**
- 在 **springMVCDemo02** 应用的 src 目录下创建 **pojo** 包，并在该包中创建实体类 **UserForm**，代码如下：

```java
package pojo;
public class UserForm {
    private String uname; // 与请求参数名称相同
    private String upass;
    private String reupass;

    public String getUname() {
        return uname;
    }

    public void setUname(String uname) {
        this.uname = uname;
    }

    public String getUpass() {
        return upass;
    }

    public void setUpass(String upass) {
        this.upass = upass;
    }

    public String getReupass() {
        return reupass;
    }

    public void setReupass(String reupass) {
        this.reupass = reupass;
    }
}
```

**4) 创建控制器类**
- 在 **springMVCDemo02** 应用的 **controller** 包中创建控制器类 **IndexController** 和 **UserController**。
- IndexController.java

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@RequestMapping("/index")
public class IndexController {
    @RequestMapping("/login")
    public String login() {
        return "login"; // 跳转到/WEB-INF/jsp下的login.jsp
    }
    @RequestMapping("/register")
    public String register() {
        return "register";
    }
}
```

- UserController.java

```java
package controller;
import javax.servlet.http.HttpSession;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import pojo.UserForm;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
@Controller
@RequestMapping("/user")
public class UserController {
    // 得到一个用来记录日志的对象，这样在打印信息的时候能够标记打印的是哪个类的信息
    private static final Log logger = LogFactory.getLog(UserController.class);
    /**
     * 处理登录 使用UserForm对象(实体Bean) user接收注册页面提交的请求参数
     */
    @RequestMapping("/login")
    public String login(UserForm user, HttpSession session, Model model) {
        if ("zhangsan".equals(user.getUname())
                && "123456".equals(user.getUpass())) {
            session.setAttribute("u", user);
            logger.info("成功");
            return "main"; // 登录成功，跳转到 main.jsp
        } else {
            logger.info("失败");
            model.addAttribute("messageError", "用户名或密码错误");
            return "login";
        }
    }
    /**
     * 处理注册 使用UserForm对象(实体Bean) user接收注册页面提交的请求参数
     */
    @RequestMapping("/register")
    public String register(UserForm user, Model model) {
        if ("zhangsan".equals(user.getUname())
                && "123456".equals(user.getUpass())) {
            logger.info("成功");
            return "login"; // 注册成功，跳转到 login.jsp
        } else {
            logger.info("失败");
            // 在register.jsp页面上可以使用EL表达式取出model的uname值
            model.addAttribute("uname", user.getUname());
            return "register"; // 返回 register.jsp
        }
    }
}
```

**5) 创建页面视图**
- 在 **springMVCDemo02** 应用的 **/WEB-INF/jsp** 目录下创建 **register.jsp** 和 **login.jsp**。

- register.jsp

```
<%@ page language="java" contentType="text/html; charset=UTF-8"
         pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Insert title here</title>
</head>
<body>
<form action="${pageContext.request.contextPath }/user/register" method="post" name="registForm">
    <table border=1 bgcolor="lightblue" align="center">
        <tr>
            <td>姓名：</td>
            <td>
                <input class="textSize" type="text" name="uname" value="${uname}" />
            </td>
        </tr>
        <tr>
            <td>密码：</td>
            <td>
                <input class="textSize" type="password" maxlength="20" name="upass" />
            </td>
        </tr>
        <tr>
            <td>确认密码：</td>
            <td>
                <input class="textSize" type="password" maxlength="20" name="reupass" />
            </td>
        </tr>
        <tr>
            <td colspan="2" align="center">
                <input type="image" src="${pageContext.request.contextPath }/images/ok.gif" onclick="go()">
            </td>
        </tr>
        </tab1e>
</form>
</body>
</html>
```

- login.jsp

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
<form action="${pageContext.request.contextPath }/user/login" method="post">
    <table>
        <tr>
            <td>姓名：</td>
            <td>
                <input type="text" name="uname" class="textSize">
            </td>
        </tr>
        <tr>
            <td>密码：</td>
            <td>
                <input type="password" name="upass" class="textsize">
            </td>
        </tr>
        <tr>
            <td colspan="2">
                <input type="image" src="${pageContext.request.contextPath }/images/ok.gif" onclick="go()">
                <input type="image" src="${pageContext.request.contextPath }/images/cancel.gif" onclick="cancel()">
            </td>
        </tr>
    </table>
    ${messageError }
</form>
</body>
</html>
```

**6) 测试应用**
- 运行 **springMVCDemo02** 应用的首页面，进行程序测试。

## 4.2 通过处理方法的形参接收请求参数
- 接收参数方式适用于 get 和 post 提交请求方式。
- 通过处理方法的形参接收请求参数也就是直接把表单参数写在控制器类相应方法的形参中，即**形参名称与请求参数名称完全相同**。

**1) 修改register方法**
- 将“通过实体 Bean 接收请求参数”部分中控制器类 UserController 中 register 方法的代码修改如下：

```java

    /**
    * 通过形参接收请求参数，形参名称与请求参数名称完全相同
    */
@RequestMapping("/register")
public String register(String uname,String upass,Model model) {
    if ("zhangsan".equals(uname)
            && "123456".equals(upass)) {
        logger.info("成功");
        return "login"; // 注册成功，跳转到 login.jsp
    } else {
        logger.info("失败");
        // 在register.jsp页面上可以使用EL表达式取出model的uname值
        model.addAttribute("uname", uname);
        return "register"; // 返回 register.jsp
    }
}
```

## 4.3 通过 HttpServletRequest 接收请求参数
- 通过 HttpServletRequest 接收请求参数适用于 get 和 post 提交请求方式。

**1) 修改register方法**
- 将“通过实体 Bean 接收请求参数”部分中控制器类 UserController 中 register 方法的代码修改如下：

```java
    /**
     * 通过HttpServletRequest接收请求参数
     */
@RequestMapping("/register")
public String register(HttpServletRequest request, Model model) {
    String uname = request.getParameter("uname");
    String upass = request.getParameter("upass");
    if ("zhangsan".equals(uname)
            && "123456".equals(upass)) {
        logger.info("成功");
        return "login"; // 注册成功，跳转到 login.jsp
    } else {
        logger.info("失败");
        // 在register.jsp页面上可以使用EL表达式取出model的uname值
        model.addAttribute("uname", uname);
        return "register"; // 返回 register.jsp
    }
}
```

## 4.4 通过 @PathVariable 接收 URL 中的请求参数
- 通过 @PathVariable 获取 URL 中的参数，控制器类示例代码如下：

## 4.5 通过 @RequestParam 接收请求参数
- 通过 @RequestParam 接收请求参数适用于 get 和 post 提交请求方式。

**1) 修改register方法**
- 将“通过实体 Bean 接收请求参数”部分控制器类 UserController 中 register 方法的代码修改如下：

```java
    /**
     * 通过@RequestParam接收请求参数
     */
@RequestMapping("/register")
public String register(@RequestParam String uname,
                           @RequestParam String upass, Model model) {
    if ("zhangsan".equals(uname) && "123456".equals(upass)) {
        logger.info("成功");
        return "login"; // 注册成功，跳转到 login.jsp
    } else {
        // 在register.jsp页面上可以使用EL表达式取出model的uname值
        model.addAttribute("uname", uname);
        return "register"; // 返回 register.jsp
    }
}
```
- 通过 @RequestParam 接收请求参数与“通过处理方法的形参接收请求参数”部分的区别如下：当请求参数与接收参数名不一致时，“通过处理方法的形参接收请求参数”不会报 404 错误，而“通过 @RequestParam 接收请求参数”会报 404 错误。

## 4.6 通过 @ModelAttribute 接收请求参数
- 通过 @ModelAttribute 注解接收请求参数适用于 get 和 post 提交请求方式。
- 当 @ModelAttribute 注解放在处理方法的形参上时，用于将多个请求参数封装到一个实体对象，从而简化数据绑定流程，而且自动暴露为模型数据，在视图页面展示时使用。
- 将“通过实体 Bean 接收请求参数”中控制器类 UserController 中 register 方法的代码修改如下：

```java
    /**
     * 通过@ModelAttribute接收请求参数
     */
@RequestMapping("/register")
public String register(@ModelAttribute("user") UserForm user) {
    if ("zhangsan".equals(user.getUname()) && "123456".equals(user.getUpass())) {
        logger.info("成功");
        return "login"; // 注册成功，跳转到 login.jsp
    } else {
        logger.info("失败");
        // 使用@ModelAttribute("user")与model.addAttribute("user",user)的功能相同
        //register.jsp页面上可以使用EL表达式${user.uname}取出ModelAttribute的uname值
        return "register"; // 返回 register.jsp 
        // 
    }
}
```

# 第5章 Spring MVC的转发与重定向
- **重定向**是将用户从当前处理请求定向到另一个视图（例如 JSP）或处理请求，以前的请求（request）中存放的信息全部失效，并进入一个新的 request 作用域。
- **转发**是将用户对当前处理的请求转发给另一个视图或处理请求，以前的 request 中存放的信息不会失效。
- 转发是服务器行为，重定向是客户端行为。

## 5.1 转发过程 
- 客户浏览器发送 http 请求，Web 服务器接受此请求，调用内部的一个方法在容器内部完成请求处理和转发动作，将目标资源发送给客户；在这里转发的路径必须是同一个 Web 容器下的 URL，其不能转向到其他的 Web 路径上，中间传递的是自己的容器内的 request。

## 5.2 重定向过程 
- 客户浏览器发送 http 请求，Web 服务器接受后发送 302 状态码响应及对应新的 location 给客户浏览器，客户浏览器发现是 302 响应，则自动再发送一个新的 http 请求，请求 URL 是新的 location 地址，服务器根据此请求寻找资源并发送给客户。

## 5.3 代码示例

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@RequestMapping("/index")
public class IndexController {
    @RequestMapping("/login")
    public String login() {
        //转发到一个请求方法（同一个控制器类可以省略/index/）
        return "forward:/index/isLogin";
    }
    @RequestMapping("/isLogin")
    public String isLogin() {
        //重定向到一个请求方法
        return "redirect:/index/isRegister";
    }
    @RequestMapping("/isRegister")
    public String isRegister() {
        //转发到一个视图
        return "register";
    }
}
```

# 第6章 Spring MVC依赖注入及@ModelAttribute注解的使用
- 在前面学习的控制器中并没有体现 MVC 的 M 层，这是因为控制器既充当 C 层又充当 M 层。这样设计程序的系统结构很不合理，应该将 M 层从控制器中分离出来。
- 下面将第4章《Spring MVC 获取参数》中“登录”和“注册”的业务逻辑处理分离出来，使用 Service 层实现。

## 6.1 依赖注入详细步骤
**1) 创建service包**
- 首先创建 **service** 包，在该包中创建 **UserService** 接口和 **UserServiceImpl** 实现类。
- UserService.java

```java
package service;
import pojo.UserForm;
public interface UserService {
    boolean login(UserForm user);
    boolean register(UserForm user);
}
```

- UserServiceImpl.java

```java
package service;
import org.springframework.stereotype.Service;
import pojo.UserForm;
//注解为一个服务
@Service
public class UserServiceImpl implements UserService {
    public boolean login(UserForm user) {
        if ("zhangsan".equals(user.getUname())
                && "123456".equals(user.getUpass())) {
            return true;
        }
        return false;
    }
    public boolean register(UserForm user) {
        if ("zhangsan".equals(user.getUname())
                && "123456".equals(user.getUpass())) {
            return true;
        }
        return false;
    }
}
```

**2) 修改配置文件**
- 在配置文件中添加一个 <context：component-scan base-package=“基本包”/>元素，具体代码如下：

```xml
<context:component-scan base-package="service"/>
```

**3) 修改控制器类 UserController**

```java
package controller;
import javax.servlet.http.HttpSession;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;
import pojo.UserForm;
import service.UserService;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
@Controller
@RequestMapping("/user")
public class UserController {
    // 得到一个用来记录日志的对象，这样在打印信息的时候能够标记打印的是哪个类的信息
    private static final Log logger = LogFactory.getLog(UserController.class);
    // 将服务依赖注入到属性userService
    @Autowired
    public UserService userService;
    /**
     * 处理登录
     */
    @RequestMapping("/login")
    public String login(UserForm user, HttpSession session, Model model) {
        if (userService.login(user)) {
            session.setAttribute("u", user);
            logger.info("成功");
            return "main"; // 登录成功，跳转到 main.jsp
        } else {
            logger.info("失败");
            model.addAttribute("messageError", "用户名或密码错误");
            return "login";
        }
    }
    /**
     * 处理注册
     */
    @RequestMapping("/register")
    public String register(@ModelAttribute("user") UserForm user) {
        if (userService.register(user)) {
            logger.info("成功");
            return "login"; // 注册成功，跳转到 login.jsp
        } else {
            logger.info("失败");
            // 使用@ModelAttribute("user")与model.addAttribute("user",user)的功能相同
            // 在register.jsp页面上可以使用EL表达式${user.uname}取出ModelAttribute的uname值
            return "register"; // 返回register.jsp
        }
    }
}
```

## 6.2 @ModelAttribute注解的使用
- 通过 **org.springframework.web.bind.annotation.ModelAttribute** 注解类型可经常实现以下两个功能。

### 6.2.1 绑定请求参数到实体对象（表单的命令对象）

```java
@RequestMapping("/register")
public String register(@ModelAttribute("user") UserForm user) {
    if ("zhangsan".equals(uname) && "123456".equals(upass)) {
        logger.info("成功");
        return "login";
    } else {
        logger.info("失败");
        return "register";
}
```

> 在上述代码中“@ModelAttribute（"user"）UserForm user”语句的功能有两个：  
将请求参数的输入封装到 user 对象中。  
创建 UserForm 实例。   

- 以“user”为键值存储在 Model 对象中，和“**model.addAttribute（"user"，user）**”语句的功能一样。如果没有指定键值，即“@ModelAttribute UserForm user”，那么在创建 UserForm 实例时以“userForm”为键值存储在 Model 对象中，和“**model.addAtttribute（"userForm", user）**”语句的功能一样。

### 6.2.2 注解一个非请求处理方法
- 被 **@ModelAttribute** 注解的方法将在每次调用该控制器类的请求处理方法前被调用。
- 
