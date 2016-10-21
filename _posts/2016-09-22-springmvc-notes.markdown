---
date: 2016-09-22 14:35
status: public
title: springmvc笔记
layout: post
---

### 一、hello spring mvc

#### 1、mvc框架要做哪些事情：

  a）将url映射到java类或java类的方法
  
  b）封装用户提交的数据
  
  c）处理请求——调用相关的业务处理——封装响应的数据
  
  d）将响应数据的数据进行渲染，jsp,html,freemaker等
  
  ![mvc结构]({{ site.url }}/images/springmvc/1.jpg)
  
#### 2、步骤：

##### a）搭建maven，导入相关jar包（maven）
  
  ![mvc结构]({{ site.url }}/images/springmvc/37.jpg)

  修改pom.xml
  
  * Spring框架jar包：

    spring-framework-4.0.2.RELEASE
    
```xml
<!-- springjar包 -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>${spring.version}</version>
</dependency>
```
    
  * Spring框架依赖的jar包：
    * 添加Apache commons logging日志，此处使用的是commons.logging-1.1.1.jar；
    * 需要添加jstl标签库支持，此处使用的是jstl-1.1.2.jar和standard-1.1.2.jar；
  
    ![properties]({{ site.url }}/images/springmvc/35.jpg)
  
    ![properties]({{ site.url }}/images/springmvc/36.jpg)
    
```xml
<!-- maven包 -->
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>3.8.1</version>
  <scope>test</scope>
</dependency>
<dependency>
  <groupId>com.sun.faces</groupId>
  <artifactId>jsf-api</artifactId>
  <version>2.0.4-b09</version>
</dependency>
<dependency>
  <groupId>com.sun.faces</groupId>
  <artifactId>jsf-impl</artifactId>
  <version>2.0.4-b09</version>
</dependency>
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>jstl</artifactId>
  <version>1.2</version>
</dependency>
<dependency>
  <groupId>taglibs</groupId>
  <artifactId>standard</artifactId>
  <version>1.1.2</version>
</dependency>
```
 
##### b）配置web.xml文件——配置分发器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee 
        http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
  <display-name>springmvc</display-name>
  <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>
```	
* load-on-startup：表示启动容器时初始化该Servlet；
* url-pattern：表示哪些请求交给Spring Web MVC处理， “/” 是用来定义默认servlet映射的。也可以如“*.html”表示拦截所有以html为扩展名的请求。

##### c）添加springmvc的配置文件，默认在WEB-INF下添加`[DispatcherServlet Name]-servlet.xml`
  *拷贝springmvc的头文件（在spring-framework-reference/html/index.html/The Web部分）*
  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p" xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
</beans>
```
  
##### d）编写HelloController.java

```java
package springmvc.controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

public class HelloWorldController implements Controller {

  public ModelAndView handleRequest(HttpServletRequest req, HttpServletResponse resp) throws Exception {
    // TODO Auto-generated method stub
    ModelAndView mv = new ModelAndView();
    mv.addObject("message", "Hello World!");
    mv.setViewName("hello");
  }
}
```	
* org.springframework.web.servlet.mvc.Controller：页面控制器/处理器必须实现Controller接口
* public ModelAndView handleRequest(HttpServletRequest req, HttpServletResponse resp) ：功能处理方法，实现相应的功能处理，比如收集参数、验证参数、绑定参数到命令对象、将命令对象传入业务对象进行业务处理、最后返回ModelAndView对象
* ModelAndView：包含了视图要实现的模型数据和逻辑视图名；
	* `mv.addObject("message", "Hello World!");`表示添加模型数据，此处可以是任意POJO对象；
	* `mv.setViewName("hello");`表示设置逻辑视图名为“hello”，视图解析器会将其解析为具体的视图，如前边的视图解析器InternalResourceVi。wResolver会将其解析为“WEB-INF/jsp/hello.jsp”。
  
##### e）编写springmvc配置文件
  
（1）配置HandlerMapping

```xml
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping" />
```
（2）配置HandlerAdapter

```xml
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
```
  		
（3）配置渲染器

(Resolving views -> jsp渲染器)
	
![渲染器]({{ site.url }}/images/springmvc/38.jpg)

```xml
<bean id="jspViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
  <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
  <property name="prefix" value="/WEB-INF/jsp/" />
  <property name="suffix" value=".jsp" />
</bean>
```
	
（4）配置请求处理器

```xml
<bean name="/hello" class="springmvc.controller.HelloWorldController" />
```		
  `name="/hello"`：前边配置的BeanNameUrlHandlerMapping，表示如过请求的URL为 “上下文/hello”，则将会交给该Bean进行处理。 
  	
#### f）创建 /WEB-INF/jsp/hello.jsp视图页面：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Hello World</title>
</head>
<body>${message }
</body>
</html>
```
  
***
  
### 二、使用注解开发springmvc

#### 1、导入jar包

#### 2、配置web.xml

#### 3、Controller

#### 4、springmvc的配置（扫描该包下的注解）

### 三、Controller配置汇总

#### 1、通过URL对应Bean

![通过URL对应Bean]({{ site.url }}/images/springmvc/2.jpg)

#### 2、为URL分配Bean

![为URL分配Bean]({{ site.url }}/images/springmvc/3.jpg)

#### 3、URL匹配Bean（不常用）

![URL匹配Bean]({{ site.url }}/images/springmvc/4.jpg)

#### 4、注解

![注解]({{ site.url }}/images/springmvc/5.jpg)

***

### 三、结果跳转方式

#### 1、设置ModelAndView对象，根据View的名称，和视图解析器跳转到指定的页面

页面：视图解析器的前缀 + view name + 视图解析器的后缀

![视图解析器]({{ site.url }}/images/springmvc/6.jpg)

#### 2、通过ServletAPI对象来实现，不需要视图解析器的配置

* 通过HttpServletResponse来进行输出

  ![httpservlet]({{ site.url }}/images/springmvc/7.jpg)

* 通过HttpServletResponse实现重定向

  ![httpservlet]({{ site.url }}/images/springmvc/8.jpg)

* 通过HttpServletRequest实现转发

  ![httpservlet]({{ site.url }}/images/springmvc/9.jpg)

* 通过springmvc来实现转发和重定向——没有视图解析器

  * 转发的实现1

    ![springmvc]({{ site.url }}/images/springmvc/10.jpg)

  * 转发的实现2
  
    ![springmvc]({{ site.url }}/images/springmvc/11.jpg)
    
  * 重定向
  
    ![springmvc]({{ site.url }}/images/springmvc/12.jpg)
    
* 通过springmvc来实现转发和重定向——有视图解析器

  * 转发方式
  
    ![springmvc]({{ site.url }}/images/springmvc/13.jpg)

  *注意：*重定向`redirect:index.jsp`不需要视图解析器
  
***

### 四、数据的处理

#### 1、提交数据的处理

a）提交的域名称和处理方法的参数名一致即可

  提交的数据

  ![data]({{ site.url }}/images/springmvc/14.jpg)

  处理方法
  
  ![data]({{ site.url }}/images/springmvc/15.jpg)
  
b）如果域名称和参数名不一致

  提交的数据
  
  ![data]({{ site.url }}/images/springmvc/16.jpg)

  处理方法
  
  ![data]({{ site.url }}/images/springmvc/17.jpg)
  
c）提交是一个对象

  要求提交的表单域名和对象的属性名一致，参数使用对象即可
  
  ![data]({{ site.url }}/images/springmvc/18.jpg)
  
  处理方法
  
  ![data]({{ site.url }}/images/springmvc/19.jpg)
  
  实体类
  
  ![data]({{ site.url }}/images/springmvc/20.jpg)
  
#### 2、将数据显示到ui层

 * 第一种通过ModelAndView——需要视图解析器
 
   ![data]({{ site.url }}/images/springmvc/21.jpg)
   
 * 第二种通过ModelMap来实现——不需要视图解析器
 
   *注意：*ModelMap需要作为处理方法的参数
   
   ![data]({{ site.url }}/images/springmvc/22.jpg)
   
 ModelView和ModelMap的区别：
 
 * 相同点：
   
   都可以将数据封装显示到表示层页面中
   
 * 不同点：
 
   * ModelAndView可以指定跳转的视图，而ModelMap不能
   * ModelAndView需要视图解析器，而ModelMap不需要配置
   
***

### 五、乱码及restful

#### 1、乱码的解决（传递参数为中文）——通过过滤器来解决乱码：在springmvc中提供CharacterEncodingFilter解决post乱码

  ——在web.xml中配置
  
  ![data]({{ site.url }}/images/springmvc/23.jpg)
  
  如果是get方式乱码

  * 修改tomcat的配置解决
  * 自定义乱码解决的过滤器
  
#### 2、restful风格的url

  优点：轻量级，安全，效率高
  
  ![data]({{ site.url }}/images/springmvc/24.jpg)
  
#### 3、同一个controller通过参数来到大不同的处理方法（不建议）

提交url:

![data]({{ site.url }}/images/springmvc/25.jpg)

处理代码：

![data]({{ site.url }}/images/springmvc/26.jpg)

***

### 六、springmvc中ajax的处理

#### 1、使用HttpServletResponse处理——不需要配置解析器

![ajax]({{ site.url }}/images/springmvc/27.jpg)

#### 2、springmvc处理json数据

a）导入jar包

  ![json]({{ site.url }}/images/springmvc/28.jpg)
  
b）配置json转换器

  ![json]({{ site.url }}/images/springmvc/29.jpg)
  
c）controller代码

  ![json]({{ site.url }}/images/springmvc/30.jpg)
  
***

### 七、拦截器

#### 1、实现拦截器

  * 实现接口HandlerIntercepter
  
    ![handlerintercepter]({{ site.url }}/images/springmvc/31.jpg)
    
    ![handlerintercepter]({{ site.url }}/images/springmvc/32.jpg)
    
  * 配置拦截器

    ![handlerintercepter]({{ site.url }}/images/springmvc/33.jpg)
        
#### 2、如果被拦截——能否达到指定的页面？
  
  使用HttpServletResponse或HttpServletRequest可以实现转发或重定向
  
  ![handlerintercepter]({{ site.url }}/images/springmvc/34.jpg)
  
#### 3、拦截器应用——登录