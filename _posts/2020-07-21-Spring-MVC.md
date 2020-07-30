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
- 创建一个 Spring MVC 应用 **springMVCDemo02** 来演示相关知识，springMVCDemo02 的 JAR 包、web.xml 与 springMVCDemo01 应用的 JAR 包、web.xml 完全一样。

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
- 这种特性可以用来控制登录权限，当然控制登录权限的方法有很多，例如拦截器、过滤器等。

# 第7章 Spring MVC数据绑定
-  在执行程序时，Spring MVC会根据客户端请求参数的不同，将**请求消息中的信息**以一定的方式**转换并绑定到控制器类的方法参数**中。这种将请求消息数据与后台方法参数建立连接的过程就是Spring MVC中的数据绑定。
-  数据绑定流程图

![image](/assets/img/post_img/DataBinding.png)

- 数据绑定流程：

> 1. Spring MVC将ServletRequest对象传递给DataBinder。
> 2. 将处理方法的入参对象传递给DataBinder。
> 3. DataBinder调用ConversionService组件进行数据类型转换、数据格式化等工作，并将ServletRequest对象中的消息填充到参数对象中。
> 4. 调用Validator组件对已经绑定了请求消息数据的参数对象进行数据合法性校验。
> 5. 检验完成后会生成数据绑定结果BindingResult对象，Spring MVC会将BindingResult对象中的内容赋给处理方法的相应参数。

- 根据客户端请求参数类型和个数的不同，将Spring MVC中的数据绑定主要分为**简单数据绑定**和**复杂数据绑定**。

## 7.1 简单数据绑定
- 当前端请求的参数比较简单时，可以在后台方法的形参中直接使用Spring MVC提供的默认参数类型进行数据绑定。
- 常用默认参数类型

### 7.1.1 绑定默认数据类型

> HttpServletRequest：通过request对象获取请求信息；  
HttpServletResponse：通过response处理响应信息；  
HttpSession：通过session对象得到session中存放的对象；  
Model/ModelMap：Model是一个接口，ModelMap是一个接口实现，作用是将model数据填充到request域。

**1) 创建项目**
- 创建一个 Spring MVC 应用 **springMVCDemo03** 来演示相关知识，springMVCDemo03 的 JAR 包、web.xml 与 springMVCDemo02 应用的 JAR 包、web.xml 完全一样。

**2) 创建Controller类**
- 在 **springMVCDemo03** 的 src 目录下创建 **controller** 包，并在该包中创建名为 **UserController**
的类，代码如下：

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import javax.servlet.http.HttpServletRequest;

@Controller
public class UserController {
    @RequestMapping("/selectUser")
    public String selectUser(HttpServletRequest request) {
        String id = request.getParameter("id");
        System.out.println("id="+id);
        return "success";
    }
}
```

**3) 运行项目**
- 发起请求 http://localhost:8080/SpringMVCDemo03/selectUser?id=1
- IDEA控制台输出结果：

> id = 1

### 7.1.2 绑定简单数据类型
- 简单数据类型的绑定，就是指Java中几种基本数据类型的绑定，例如int、String、Double等类型。

#### 7.1.2.1 参数名相同
**1) 修改Controller类**
- 将上述案例中控制器类**UserController**中的**selectUser()**方法进行修改：

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class UserController {
    @RequestMapping("/selectUser")
    public String selectUser(Integer id) {
        System.out.println("id="+id);
        return "success";
    }
}
```

**2) 运行项目**
- 发起请求 http://localhost:8080/SpringMVCDemo03/selectUser?id=1
- IDEA控制台输出结果：

> id = 1

#### 7.1.2.1 参数名不同
- 以上两个例子中，前端请求中参数名和后台控制器类方法中的形参名一样（均为id）。如若不同，这就会导致后台无法正确绑定并接收到前端请求的参数。
- 针对前端请求中参数名和后台控制器类方法中的形参名不一样的情况，可以考虑使用Spring MVC提供的**@RequestParam**注解类型来进行间接数据绑定。

**1) 修改Controller类**
- 将上述案例中控制器类**UserController**中的**selectUser()**方法进行修改：

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class UserController {
    @RequestMapping("/selectUser")
    public String selectUser(@RequestParam(value="user_id")Integer id) {
        System.out.println("id="+id);
        return "success";
    }
}
```

**2) 运行项目**
- 发起请求 http://localhost:8080/SpringMVCDemo03/selectUser?id=1
- IDEA控制台输出结果：

> id = 1

### 7.1.3 绑定POJO类型
- 针对多类型、多参数的请求，可以使用POJO类型进行数据绑定。POJO类型的数据绑定就是将所有关联的请求参数封装在一个POJO中，然后在方法中直接使用该POJO作为形参来完成数据绑定。

**1) 创建实体类**
- 在 **springMVCDemo03** 的 src 目录下创建 **pojo** 包，并在该包中创建名为 **User**
的实体类，代码如下：

```java
package pojo;

public class User {
    private Integer id;       //用户id
    private String username; //用户
    private Integer password;//用户密码

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Integer getPassword() {
        return password;
    }

    public void setPassword(Integer password) {
        this.password = password;
    }
}
```

**2) 修改Controller类**
- 将上述案例中控制器类**UserController**进行修改：

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import pojo.User;

@Controller
public class UserController {
    /** 向用户注册页面跳转 */
    @RequestMapping("/toRegister")
    public String toRegister( ) {  return "register";    }

    /** 接收用户注册信息 */
    @RequestMapping("/registerUser")
    public String registerUser(User user) {
        String username = user.getUsername();
        Integer password = user.getPassword();
        System.out.println("username="+username);
        System.out.println("password="+password);
        return "success";
    }

}
```

**3) 创建注册界面**
- 在 **springMVCDemo03** 应用的 /WEB-INF/jsp 目录下创建信息显示页面 **register.jsp**，核心代码如下：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>$Title$</title>
</head>
<body>
<form action="${pageContext.request.contextPath }/registerUser"
      method="post">
    用户名：<input type="text" name="username" /><br />
    密&nbsp;&nbsp;&nbsp;码：<input type="text" name="password" /><br />
    <input type="submit" value="注册"/>
</form>
</body>
</html>
```

**3) 运行项目**
- 发起请求 http://localhost:8080/SpringMVCDemo03/toRegister，并在前端表单输入用户名（如lam）和密码（如123），然后点击注册。
- IDEA控制台输出结果：

> username=lam  
password=123

### 7.1.4 绑定包装POJO类型
- 使用包装POJO类型绑定。所谓的包装POJO，就是在一个POJO中包含另一个简单POJO。例如，在订单对象中包含用户对象。这样在使用时，就可以通过订单查询到用户信息。

**1) 创建实体类**
- 在 **pojo** 包中创建名为 **Orders**的实体类，代码如下：

```java
package pojo;

public class Orders {
    private Integer ordersId; // 订单编号
    private User user;          // 用户POJO，所属用户
    public Integer getOrdersId() { return ordersId;  }
    public void setOrdersId(Integer ordersId) { this.ordersId = ordersId;  }
    public User getUser() { return user; }
    public void setUser(User user) { this.user = user;  }
}
```

**2) 创建Controller类**
- 在**controller** 包中创建名为 **OrdersController**的控制类，代码如下：

```java
package controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import pojo.Orders;
import pojo.User;

@Controller
public class OrdersController {
    /** 向查询订单页面跳转 */
    @RequestMapping("/toOrder")
    public String toOrder( ) {  return "order";    }

    /** 查询订单和用户信息  */
    @RequestMapping("/findOrdersWithUser")
    public String findOrdersWithUser(Orders orders) {
        Integer orderId = orders.getOrdersId();
        User user = orders.getUser();
        String username = user.getUsername();
        System.out.println("orderId=" + orderId);
        System.out.println("username=" + username);
        return "success";
    }
}
```

**3) 创建注册界面**
- 在 **springMVCDemo03** 应用的 /WEB-INF/jsp 目录下创建信息显示页面 **order.jsp**，核心代码如下：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="${pageContext.request.contextPath }/findOrdersWithUser"
      method="post">
    <!-- 参数是包装类基本属性，则直接用属性名 -->
    订单编号：<input type="text" name="ordersId" /><br />
    <!-- 参数是包装类中POJO类的子属性，则必须用【对象.属性】 -->
    所属用户：<input type="text" name="user.username" /><br />
    <input type="submit" value="查询" />
</form>
</body>
</html>
```

**4) 运行项目**
- 发起请求 http://localhost:8080/SpringMVCDemo03/toOrder，并在前端表单输入订单编号（如1）和所属用户（如lam），然后点击查询。
- IDEA控制台输出结果：

> orderId=1
username=lam

### 7.1.5 自定义数据绑定
#### 7.1.5.1 类型转换器
- Spring MVC 框架的 Converter<S，T> 是一个可以将一种数据类型转换成另一种数据类型的接口，这里 S 表示源类型，T 表示目标类型。开发者在实际应用中使用框架内置的类型转换器基本上就够了，但有时需要编写具有特定功能的类型转换器。

**1) 内置的类型转换器**
- 在 Spring MVC 框架中，对于常用的数据类型，开发者无须创建自己的类型转换器，因为 Spring MVC 框架有许多内置的类型转换器用于完成常用的类型转换。Spring MVC 框架提供的内置类型转换包括以下几种类型。

*1) 标量转换器*

<table>
  <thead>
    <tr>
      <th>名称</th>
      <th>作用</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>StringToBooleanConverter</td>
      <td>String 到 boolean 类型转换</td>
    </tr>
    <tr>
      <td>ObjectToStringConverter</td>
      <td>Object 到 String 转换，调用 toString 方法转换</td>
    </tr>
    <tr>
      <td>StringToNumberConverterFactory</td>
      <td>String 到数字转换（例如 Integer、Long 等）</td>
    </tr>
    <tr>
      <td>NumberToNumberConverterFactory</td>
      <td>数字子类型（基本类型）到数字类型（包装类型）转换</td>
    </tr>
    <tr>
      <td>StringToCharacterConverter</td>
      <td>String 到 Character 转换，取字符串中的第一个字符</td>
    </tr>
    <tr>
      <td>NumberToCharacterConverter</td>
      <td>数字子类型到 Character 转换</td>
    </tr>
    <tr>
      <td>CharacterToNumberFactory</td>
      <td>Character 到数字类型转换</td>
    </tr>
    <tr>
      <td>StringToEnumConverterFactory</td>
      <td>String 到枚举类型转换，通过 Enum.valueOf 将字符串转换为需要的枚举类型</td>
    </tr>
    <tr>
      <td>EnumToStringConverter</td>
      <td>枚举类型到 String 转换，返回枚举对象的 name 值</td>
    </tr>
    <tr>
      <td>StringToLocaleConverter</td>
      <td>String 到 java.util.Locale 转换</td>
    </tr>
    <tr>
      <td>PropertiesToStringConverter</td>
      <td>java.util.Properties 到 String 转换，默认通过 ISO-8859-1 解码</td>
    </tr>
    <tr>
      <td>StringToPropertiesConverter</td>
      <td>String 到 java.util.Properties 转换，默认使用 ISO-8859-1 编码</td>
    </tr>
  </tbody>
</table>

*2) 集合、数组相关转换器*

<table>
  <thead>
    <tr>
      <th>名称</th>
      <th>作用</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>ArrayToCollectionConverter</td>
      <td>任意数组到任意集合（List、Set）转换</td>
    </tr>
    <tr>
      <td>CollectionToArrayConverter</td>
      <td>任意集合到任意数组转换</td>
    </tr>
    <tr>
      <td>ArrayToArrayConverter</td>
      <td>任意数组到任意数组转换</td>
    </tr>
    <tr>
      <td>CollectionToCollectionConverter</td>
      <td>集合之间的类型转换</td>
    </tr>
    <tr>
      <td>MapToMapConverter</td>
      <td>Map之间的类型转换</td>
    </tr>
    <tr>
      <td>ArrayToStringConverter</td>
      <td>任意数组到 String 转换</td>
    </tr>
    <tr>
      <td>StringToArrayConverter</td>
      <td>字符串到数组的转换，默认通过“，”分割，且去除字符串两边的空格（trim）</td>
    </tr>
    <tr>
      <td>ArrayToObjectConverter</td>
      <td>任意数组到 Object 的转换，如果目标类型和源类型兼容，直接返回源对象；否则返回数组的第一个元素并进行类型转换</td>
    </tr>
    <tr>
      <td>ObjectToArrayConverter</td>
      <td>Object 到单元素数组转换</td>
    </tr>
    <tr>
      <td>CollectionToStringConverter</td>
      <td>任意集合（List、Set）到 String 转换</td>
    </tr>
    <tr>
      <td>StringToCollectionConverter</td>
      <td>String 到集合（List、Set）转换，默认通过“，”分割，且去除字符串两边的空格（trim）</td>
    </tr>
    <tr>
      <td>CollectionToObjectConverter</td>
      <td>任意集合到任意 Object 的转换，如果目标类型和源类型兼容，直接返回源对象；否则返回集合的第一个元素并进行类型转换</td>
    </tr>
    <tr>
      <td>ObjectToCollectionConverter</td>
      <td>Object 到单元素集合的类型转换</td>
    </tr>
  </tbody>
</table>

**2) 自定义类型转换器**
- 当 Spring MVC 框架内置的类型转换器不能满足需求时，开发者可以开发自己的类型转换器。

> 例如有一个应用 springMVCDemo04 希望用户在页面表单中输入信息来创建商品信息。当输入“apple，10.58，200”时表示在程序中自动创建一个 new Goods，并将“apple”值自动赋给 goodsname 属性，将“10.58”值自动赋给 goodsprice 属性，将“200”值自动赋给 goodsnumber 属性。

> 如果想实现上述应用，需要做以下 5 件事：
> 1. 创建实体类。
> 2. 创建控制器类。
> 3. 创建自定义类型转换器类。
> 4. 注册类型转换器。
> 5. 创建相关视图。

**1) 创建项目**
- 创建一个 Spring MVC 应用 **springMVCDemo04** 来演示相关知识，springMVCDemo04 的 JAR 包、web.xml 与 springMVCDemo03 应用的 JAR 包、web.xml 完全一样。

**2) 创建实体类**
- 在 **springMVCDemo04** 的 src 目录下创建 **pojo** 包，并在该包中创建名为 **GoodsModel**
的实体类，代码如下：

```java
package pojo;
public class GoodsModel {
    private String goodsname;
    private double goodsprice;
    private int goodsnumber;

    public String getGoodsname() {
        return goodsname;
    }

    public void setGoodsname(String goodsname) {
        this.goodsname = goodsname;
    }

    public double getGoodsprice() {
        return goodsprice;
    }

    public void setGoodsprice(double goodsprice) {
        this.goodsprice = goodsprice;
    }

    public int getGoodsnumber() {
        return goodsnumber;
    }

    public void setGoodsnumber(int goodsnumber) {
        this.goodsnumber = goodsnumber;
    }
}
```

**3) 创建控制器类**
- 在 **springMVCDemo04** 的 src 目录下创建 **controller** 包，并在该包中创建名为 **ConverterController** 的控制器类，代码如下：

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import pojo.GoodsModel;
@Controller
@RequestMapping("/my")
public class ConverterController {
    @RequestMapping("/converter")
    /*
     * 使用@RequestParam
     * ("goods")接收请求参数，然后调用自定义类型转换器GoodsConverter将字符串值转换为GoodsModel的对象gm
     */
    public String myConverter(@RequestParam("goods") GoodsModel gm, Model model) {
        model.addAttribute("goods", gm);
        return "showGoods";
    }
}
```

**4) 创建自定义类型转换器类**
- 自定义类型转换器类需要实现 **Converter<S,T>** 接口，重写 **convert(S)** 接口方法。
- convert(S) 方法的功能是将源数据类型 S 转换成目标数据类型 T。
- 在 **springMVCDemo03** 的 src 目录下创建 **converter** 包，并在该包中创建名为 **GoodsConverter** 的自定义类型转换器类，代码如下：

```java
package converter;
import org.springframework.core.convert.converter.Converter;
import pojo.GoodsModel;

import java.util.Arrays;

public class GoodsConverter implements Converter<String, GoodsModel> {
    public GoodsModel convert(String source) {
        // 创建一个Goods实例
        GoodsModel goods = new GoodsModel();
        // 以“，”分隔
        String stringvalues[] = source.split(",");
        if (stringvalues != null && stringvalues.length == 3) {
            // 为Goods实例赋值
            System.out.println(Arrays.toString(stringvalues));
            goods.setGoodsname(stringvalues[0]);
            goods.setGoodsprice(Double.parseDouble(stringvalues[1]));
            goods.setGoodsnumber(Integer.parseInt(stringvalues[2]));
            return goods;
        } else {
            throw new IllegalArgumentException(String.format(
                    "类型转换失败， 需要格式'apple,10.58,200 ',但格式是[% s ] ", source));
        }
    }
}
```

**5) 注册类型转换器**
- 在 **springMVCDemo04** 的 WEB-INF 目录下创建配置文件 **springmvc-servlet.xml**，并在配置文件中注册自定义类型转换器，配置文件代码如下：

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
    <mvc:annotation-driven conversion-service="conversionService"/>
    <!--注册类型转换器GoodsConverter-->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <list>
                <bean class="converter.GoodsConverter"/>
            </list>
        </property>
    </bean>
    <bean
            class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

**6) 创建相关视图**
- 在 **springMVCDemo04** 应用的 web 目录下创建信息采集页面 **index.jsp**，核心代码如下：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>
  <form action="${pageContext.request.contextPath}/my/converter" method= "post">
    请输入商品信息（格式为apple,10.58,200）:
    <input type="text" name="goods" /><br>
    <input type="submit" value="提交" />
  </form>
  </body>
</html>
```

- 在 **springMVCDemo04** 应用的 /WEB-INF/jsp 目录下创建信息显示页面 **showGoods.jsp**，核心代码如下：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
您创建的商品信息如下：
<!-- 使用EL表达式取出model中的goods信息 -->
商品名称为:${goods.goodsname }
商品价格为:${goods.goodsprice }
商品名称为:${goods.goodsnumber }
</body>
</html>
```

#### 7.1.5.2 格式化转换器
- Spring MVC 框架的 Formatter<T> 与 Converter<S，T> 一样，也是一个可以将一种数据类型转换成另一种数据类型的接口。不同的是，Formatter<T> 的源数据类型必须是 String 类型，而 Converter<S，T> 的源数据类型是任意数据类型。
- 在 Web 应用中由 HTTP 发送的请求数据到控制器中都是以 String 类型获取，因此在 Web 应用中选择 Formatter<T> 比选择 Converter<S，T> 更加合理。

**1) 内置的格式化转换器**

<table>
  <thead>
    <tr>
      <th>名称</th>
      <th>作用</th>
    </tr>
  </thead>
    <tbody>
    <tr>
      <td>NumberFormatter</td>
      <td>实现 Number 与 String 之间的解析与格式化</td>
    </tr>
    <tr>
      <td>CurrencyFormatter</td>
      <td>实现 Number 与 String 之间的解析与格式化（带货币符号）</td>
    </tr>
    <tr>
      <td>PercentFormatter</td>
      <td>实现 Number 与 String 之间的解析与格式化（带百分数符号）</td>
    </tr>
    <tr>
      <td>DateFormatter</td>
      <td>实现 Date 与 String 之间的解析与格式化</td>
    </tr>
  </tbody>
</table>

**2) 自定义格式化转换器**
- 自定义格式化转换器就是编写一个实现 org.springframework.format.Formatter 接口的 Java 类。该接口声明如下：

```java
public interface Formatter<T>
```

-  T 表示由字符串转换的目标数据类型。该接口有 parse 和 print 两个接口方法，自定义格式化转换器类必须覆盖它们。

```java
public T parse(String s,java.util.Locale locale)
public String print(T object,java.util.Locale locale)
```

> 如果想实现自定义格式化转换器，需要做以下 5 件事：
> 1. 创建实体类；
> 2. 创建控制器类；
> 3. 创建自定义格式化转换器类；
> 4. 注册格式化转换器；
> 5. 创建相关视图。

**1) 创建项目**
- 创建一个 Spring MVC 应用 **springMVCDemo05** 来演示相关知识，springMVCDemo05 的 JAR 包、web.xml 与 springMVCDemo04 应用的 JAR 包、web.xml 完全一样。

**2) 创建实体类**
- 在 **springMVCDemo05** 的 src 目录下创建 **pojo** 包，并在该包中创建名为 **GoodsModel** 的实体类，代码如下：

```java
package pojo;
import java.util.Date;
public class GoodsModel {
    private String goodsname;
    private double goodsprice;
    private int goodsnumber;
    private Date goodsdate;

    public String getGoodsname() {
        return goodsname;
    }

    public void setGoodsname(String goodsname) {
        this.goodsname = goodsname;
    }

    public double getGoodsprice() {
        return goodsprice;
    }

    public void setGoodsprice(double goodsprice) {
        this.goodsprice = goodsprice;
    }

    public int getGoodsnumber() {
        return goodsnumber;
    }

    public void setGoodsnumber(int goodsnumber) {
        this.goodsnumber = goodsnumber;
    }

    public Date getGoodsdate() {
        return goodsdate;
    }

    public void setGoodsdate(Date goodsdate) {
        this.goodsdate = goodsdate;
    }
}
```

**3) 创建控制器类**
- 在 **springMVCDemo05** 的 src 目录下创建 **controller** 包，并在该包中创建名为 **FormatterController** 的控制器类，代码如下：

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import pojo.GoodsModel;
@Controller
@RequestMapping("/my")
public class FormatterController {
        @RequestMapping("/formatter")
    public String myConverter(GoodsModel gm, Model model) {
        model.addAttribute("goods", gm);
        return "showGoods";
    }
}
```

**4) 创建自定义格式化转换器类**
- 在 **springMVCDemo05** 的 src 目录下创建 **formatter** 包，并在该包中创建名为 **MyFormatter** 的自定义格式化转换器类，代码如下：

```java
package formatter;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Locale;
import org.springframework.format.Formatter;
public class MyFormatter implements Formatter<Date> {
    SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
    public String print(Date object, Locale arg1) {
        return dateFormat.format(object);
    }
    public Date parse(String source, Locale arg1) throws ParseException {
        return dateFormat.parse(source); // Formatter只能对字符串转换
    }
}
```

**5) 注册格式化转换器**
- 在 **springMVCDemo05**  的 WEB-INF 目录下创建配置文件 **springmvc-servlet.xml**，并在配置文件中注册格式化转换器，具体代码如下：

```java
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
    <!-- 使用扫描机制扫描controller包 -->
    <context:component-scan base-package="controller" />
    <mvc:annotation-driven conversion-service="conversionService"/>
    <!--注册MyFormatter-->
    <bean id="conversionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
        <property name="formatters">
            <list>
                <bean class="formatter.MyFormatter"/>
            </list>
        </property>
    </bean>
    <mvc:annotation-driven conversion-service="conversionService"/>
    <bean
            class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

**6) 创建相关视图**
- 在 **springMVCDemo05**  应用的 web 目录下创建信息输入页面 **index.jsp**，核心代码如下：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
         pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>添加商品信息</title>
</head>
<body>
<form action="${pageContext.request.contextPath}/my/formatter" method= "post">
  <table border=1 bgcolor="lightblue" align="center">
    <tr>
      <td>商品名称：</td>
      <td><input class="textSize" type="text" name="goodsname" /></td>
    </tr>
    <tr>
      <td>商品价格：</td>
      <td><input class="textSize" type="text" name="goodsprice" /></td>
    </tr>
    <tr>
      <td>商品数量：</td>
      <td><input class="textSize" type="text" name="goodsnumber" /></td>
    </tr>
    <tr>
      <td>商品日期：</td>
      <td><input class="textSize" type="text" name="goodsdate" />（yyyy-MM-dd）</td>
    </tr>
    <tr>
      <td colspan="2" align="center">
        <input type="submit" value="提交" />
      </td>
    </tr>
    </tab1e>
</form>
</body>
</html>
```

- 在 **springMVCDemo05** 应用的 /WEB-INF/jsp 目录下创建信息显示页面 **showGoods.jsp**，核心代码如下：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
您创建的商品信息如下：<br/>
<!-- 使用EL表达式取出Action类的属性goods的值 -->
商品名称为：${goods.goodsname }<br/>
商品价格为：${goods.goodsprice }<br/>
商品名称为：${goods.goodsnumber }<br/>
商品日期为：${goods.goodsdate}
</body>
</html>
```



