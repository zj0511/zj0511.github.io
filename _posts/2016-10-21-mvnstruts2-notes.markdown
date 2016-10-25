---
date: 2016-10-21 10:38
status: draft
title: struts2基础
layout: post
---

# 第一个Stuts2程序

[struts2配置参考页面](http://www.programgo.com/article/72433346964/)

struts2框架主要通过一个过滤器将Struts集成到Web应用，这个过滤器对象是`org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter`。通过该过滤器对象，Struts2就可以获得Web应用中的HTTP请求，并将这个HTTP请求转发到指定的Action进行处理，Action根据结果返回给客户端相应的页面。

（1）创建Web项目，将Struts的支持类型库文件添加到项目中。

![basestruts2]({{ site.url }}/images/basestruts2/1.jpg)
pom.xml：

```xml
<!-- struts2依赖包 -->
<dependency>
  <groupId>org.apache.struts</groupId>
  <artifactId>struts2-core</artifactId>
  <version>2.3.14</version>
</dependency>
```

（2）在web.xml文件中声明Struts2提供的过滤器，类名为org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter：

```xml
<!DOCTYPE web-app PUBLIC
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <!-- Struts2 过滤器 -->
  <filter>
    <!-- 过滤器名称 -->
    <filter-name>struts2</filter-name>
    <!-- 过滤器类 -->
    <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
  </filter>
  <!-- Struts2 过滤器映射 -->
  <filter-mapping>
    <!-- 过滤器名称 -->
    <filter-name>struts2</filter-name>
    <!-- 过滤器映射 -->
    <url-pattern>/*</url-pattern>
  </filter-mapping>
</web-app>

```

(3)在Web项目的源码文件夹（src/main/resources）中，创建名称为`struts.xml`的配置文件，此配置文件中定义Struts2中的Action对象：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC  
    "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN"  
    "http://struts.apache.org/dtds/struts-2.1.dtd">  
 <struts>
 <!-- 声明包 -->
  <package name="myPackage" extends="struts-default">
    <!-- 定义action -->
    <action name="first">
      <!-- 定义处理成功后的映射页面 -->
      <result>/first.jsp</result>
    </action>
  </package>
 </struts>
```

* < package > 标签：声明一个包，通过name属性指定包的名称为myPackage，并通过extends属性指定此包继承于struts-default包。
* < action > 标签：用于定义Action对象，它的name属性用于指定访问此Action的URL。
* < result > 元素：用于定义处理结果和资源之间的映射关系，实例中\<result\>子元素的配置为处理成功后，请求将其转发到first.jsp页面。

（4）创建程序中的主页面index.jsp。在该页面中编写一个超链接，用于访问上面所定义的Action对象，此链接所指向的地址为first.action：

```jsp
<html>
<body>
<h2>Hello World!</h2>
<a href="first.action">struts2</a>
</body>
</html>
```

（5）创建first.jsp页面，此页面是Action对象first处理成功后的返回页面：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>struts2</title>
</head>
<body>
<h1>Hello Struts2!</h1>
</body>
</html>
```

# Action对象

主要用于对HTTP请求进行处理，在Struts2 API中，Action对象是一个接口，它位于`com.opensymphony.xwork2`包中。在Struts2项目开发中，创建Action对象都要直接或间接实现此对象。

Action接口中包含5个静态的成员变量，它们是Struts2 API为处理结果定义的静态变量：

* SUCCESS
  代表Action执行成功的返回值。如在Action执行成功的情况下，需要返回到成功页面，此时就可以将返回值设置为SUCCESS。
  
* NONE
  Action执行成功的返回值。但不需要返回到成功页面。主要用于处理不需要返回结果页面的业务逻辑。
  
* ERROR
  Action执行失败的返回值。在一些信息验证失败的情况下，可以使Action返回此值。
  
* INPUT
  代表需要返回到某个输入信息的页面的返回值。
  
* LOGIN
  代表需要用户登录的返回值。在验证用户是否登录时，Action验证失败，需要用户重新登录，此时就可以将Action对象处理的返回值设置为LOGIN。

(一)请求参数的注入原理

在Struts2框架中，表单提交的数据会自动注入到与Action对象相对应的属性。通过Action对象为属性提供setter方法进行注入：

src/main/java/com.zj.action/UserAction.java:

```java
package com.zj.action;

import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionSupport;

/**
 * 用户 Action
 *
 * @author zhangjuan
 *
 */

public class UserAction extends ActionSupport {
  private static final long serialVersionUID = 1L;

  // 用户名属性
  private String userName;

  // 为 userName 提供 getter 方法

  public String getUserName() {
    return this.userName;
  }

  // 为 userName 提供 setter 方法
  public void setUserName(final String userName) {
    this.userName = userName;
  }

  @Override
  public String execute() {
    return Action.SUCCESS;
  }
}

```

要注入属性值的Action对象，必须对属性提供setXXX()方法，Struts2的内部实现按照JavaBean规范中提供的setter方法，自动为属性注入值。在获取属性的数值时，通过getter方法获取。

（二）Action的基本流程

* Struts2框架的工作，主要是通过Struts2的过滤器对象拦截HTTP请求，然后将请求分配到指定的Action进行处理。
* Struts2的内部处理机制会判断HTTP请求是否与某个Action对象相匹配，如果找到匹配的Action，就会调用Action对象的execute()方法，并根据处理结果返回相应的值。
* Struts2通过Action的返回值查找返回值所映射的页面。

在Struts2框架中，一个`.*action`请求的返回视图由Action对象决定，其实现手段是通过查找返回的字符串对应的配置项，确定返回的视图，如Action中execute()方法返回的字符串为success，那么Struts2就会到配置文件中查找名称为`success`的配置项，返回这个配置项对应的视图。

src/main/resources/struts.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC  
    "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN"  
    "http://struts.apache.org/dtds/struts-2.1.dtd">  
 <struts>
 <!-- 声明包 -->
  <package name="myPackage" extends="struts-default">
    <!-- 定义action -->
    <action name="UserAction" class="com.zj.action.UserAction">
      <!-- 定义处理成功后的映射页面 -->
      <result>user_add.jsp</result>
    </action>
    
  </package>
 </struts>
```

src/main/web-app/index.jsp：

```jsp
<html>
<body>
	<h2>Hello World!</h2>
	<a href="UserAction">userAdd</a>
</body>
</html>
```

src/main/web-app/user_add.jsp：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>userAdd</title>
</head>
<body>
	Hello userAdd!
</body>
</html>
```

![basestruts2]({{ site.url }}/images/basestruts2/2.jpg)

（三）动态Action

![basestruts2]({{ site.url }}/images/basestruts2/3.jpg)

动态Action的处理方式，是通过请求Action对象中一个具体的方法来实现动态的操作，通过在请求Action的URL地址后方加上请求字符串（方法名称），与Action对象中的方法进行匹配，Action地址与请求字符串之间以"!"进行分割。

例如在配置文件struts.xml中配置userAction，当请求其中的add()方法时，其请求方式：
`/userAction!add`

（1）创建Web项目，将Struts的支持类型库文件添加到项目中：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.zj.mvnbook</groupId>
  <artifactId>HelloWorld</artifactId>
  <packaging>war</packaging>
  <version>0.0.1-SNAPSHOT</version>
  <name>HelloWorld Maven Webapp</name>
  <url>http://maven.apache.org</url>

  <!-- 属性配置 -->
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>

    <!-- struts2依赖包 -->
    <dependency>
      <groupId>org.apache.struts</groupId>
      <artifactId>struts2-core</artifactId>
      <version>2.3.14</version>
    </dependency>
  </dependencies>

  <build>
    <finalName>HelloWorld</finalName>
  </build>
</project>
```

（2）在web.xml文件中注册Struts2提供的过滤器：

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <!-- Struts2 过滤器 -->
  <filter>
    <!-- 过滤器名称 -->
    <filter-name>struts2</filter-name>
    <!-- 过滤器类 -->
    <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
  </filter>
  <!-- Struts2 过滤器映射 -->
  <filter-mapping>
    <!-- 过滤器名称 -->
    <filter-name>struts2</filter-name>
    <!-- 过滤器映射 -->
    <url-pattern>/*</url-pattern>
  </filter-mapping>
</web-app>
```

（3）创建名称为UserAction的Action对象，并在这个对象中分别编写add()方法与update()方法，用于处理添加用户信息的请求及更新用户信息的请求，并将请求返回到相应的页面：

```java
package com.zj.action;

import com.opensymphony.xwork2.ActionSupport;

/**
 * 用户 Action
 *
 * @author zhangjuan
 *
 */

public class UserAction extends ActionSupport {
  private static final long serialVersionUID = 1L;

  // 提示信息
  private String info;

  /**
   * 用于处理添加用户信息的请求
   * 
   * @return
   */
  public String add() {
    this.info = "添加用户信息";
    return "add";
  }

  /**
   * 用于处理更新用户信息的请求
   * 
   * @return
   */
  public String update() {
    this.info = "更新用户信息";
    return "update";
  }

  public String getInfo() {
    return this.info;
  }

  public void setInfo(final String info) {
    this.info = info;
  }

}
```

（4）在src/main/resources文件夹下创建名称为struts.xml的配置文件，在此配置文件中配置UserAction：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC  
    "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN"  
    "http://struts.apache.org/dtds/struts-2.1.dtd">  
 <struts>
 <!-- 声明包 -->
  <package name="myPackage" extends="struts-default">
    <!-- 定义action -->
    <action name="UserAction" class="com.zj.action.UserAction">
        <!-- 返回结果为add时，对应添加成功的映射页面 -->
        <result name="add">user_add.jsp</result>
        <!-- 返回结果为update时，对应更新成功的映射页面 -->
        <result name="update">user_update.jsp</result>
    </action>    
  </package>
 </struts>
```

（5）创建名为user_add.jsp页面：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="s" uri="/struts-tags" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>userAdd</title>
</head>
<body>
	<font color="red">
	<s:property value="info"/>
	</font>
</body>
</html>
```

（6）创建名称为user_update.jsp：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="s" uri="/struts-tags" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>userUpdate</title>
</head>
<body>
	<font color="blue">
	<s:property value="info"/>
	</font>
</body>
</html>
```

上述通过Sturts2标签输出UserAction中的信息：
`<%@ taglib prefix="s" uri="/struts-tags" %>`

（7）创建index.jsp：

```jsp
<html>
<body>
	<h2>Hello World!</h2>
	<a href="UserAction!add">userAdd</a>
	<br>
	<a href="UserAction!update">userUpdate</a>
</body>
</html>
```

![basestruts2]({{ site.url }}/images/basestruts2/4.jpg)

![basestruts2]({{ site.url }}/images/basestruts2/5.jpg)

***

# Struts2的配置文件

![basestruts2]({{ site.url }}/images/basestruts2/6.jpg)

（1）Struts2的包配置：

配置文件struts.xml中的包使用 < package > 元素声明，主要用于放置一些项目中的相关配置，已经配置好的包可以被其他包所继承，从而提高配置文件的重用性。

使用 < package > 元素进行声明，必须拥有一个name属性来指定包的名称：

![basestruts2]({{ site.url }}/images/basestruts2/7.jpg)

（2）名称空间配置：

在Java Web开发中，Web文件目录通常以模块进行划分，如用户将jsp文件统一放置在jsp文件夹"/jsp"中，其访问地址为"jsp/insex.jsp"，此时使用名称空间的功能：

![basestruts2]({{ site.url }}/images/basestruts2/8.jpg)

用于指定一个Action对象的访问路径。在配置文件struts.xml的包声明中，使用namespace属性进行声明，名称空间的值需要以"/"开头：

```xml
<struts>
 <!-- 声明包 -->
  <package name="userPackage" extends="struts-default" namespace="/jsp">
    <!-- 定义action -->
    ...
  </package>
 </struts>
```

（3）Action相关配置：

Struts2框架通过Action对象处理HTTP请求，其请求地址的映射需要配置在struts.xml文件中：

```xml
<!-- 定义action -->
    <action name="addUser" class="com.zj.action.UserAction" method="add">
        <!-- 添加成功的映射页面 -->
        <result>user_add.jsp</result>
    </action>
    <action name="updateUser" class="com.zj.action.UserAction" method="update">
        <!-- 更新成功的映射页面 -->
        <result>user_update.jsp</result>
    </action>    
```

![basestruts2]({{ site.url }}/images/basestruts2/9.jpg)

一个Action对象包含多个业务逻辑请求分支的情况：

* src/main/java/com.zj.action/UserAction.java：

```java
package com.zj.action;

import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionSupport;

/**
 * 用户 Action
 *
 * @author zhangjuan
 *
 */

public class UserAction extends ActionSupport {
  private static final long serialVersionUID = 1L;

  // 提示信息
  private String info;

  /**
   * 用于处理添加用户信息的请求
   *
   * @return
   */
  public String add() {
    this.info = "添加用户信息";
    return Action.SUCCESS;
  }

  /**
   * 用于处理更新用户信息的请求
   *
   * @return
   */
  public String update() {
    this.info = "更新用户信息";
    return Action.SUCCESS;
  }

  public String getInfo() {
    return this.info;
  }

  public void setInfo(final String info) {
    this.info = info;
  }

}
```

* src/main/resources/struts.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC  
    "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN"  
    "http://struts.apache.org/dtds/struts-2.1.dtd">  
 <struts>
 <!-- 声明包 -->
  <package name="userPackage" extends="struts-default" namespace="/jsp">
    <!-- 定义action -->
    <action name="addUser" class="com.zj.action.UserAction" method="add">
        <!-- 添加成功的映射页面 -->
        <result>user_add.jsp</result>
    </action>
    <action name="updateUser" class="com.zj.action.UserAction" method="update">
        <!-- 更新成功的映射页面 -->
        <result>user_update.jsp</result>
    </action>      
  </package>
 </struts>
```

* src/main/webapp/jsp/index.jsp：

```jsp
<html>
<body>
	<h2>Hello World!</h2>
	<a href="addUser">userAdd</a>
	<br>
	<a href="updateUser">userUpdate</a>
</body>
</html>
```

* src/main/webapp/user_add.jsp：

```jsp
<<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="s" uri="/struts-tags" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>userAdd</title>
</head>
<body>
	<font color="red">
		<s:property value="info"/>
	</font>	
</body>
</html>
```

* src/main/webapp/user_update.jsp：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="s" uri="/struts-tags"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>userUpdate</title>
</head>
<body>
	<font color="blue">
		<s:property value="info"/>
	</font>	
</body>
</html>
```

（4）通配符实现简化配置

+ 通配符"*": 匹配0或多个字符。
+ 通配符"\"：是一个转义字符，如需要匹配"/"，则使用"\/"进行匹配

< action > 元素的name属性的值为"*User"，匹配的是以字符"User"结尾的字符串，如"addUser"，"updateUser"。对于通配符所匹配的字符，用表达式\{1\}、\{2\}来获取。上述例子可改为：

```xml
<struts>
  <!-- 声明包 -->
  <package name="userPackage" extends="struts-default" namespace="/jsp">
    <!-- 定义action -->
    <action name="*User" class="com.zj.action.UserAction" method="{1}">
      <!-- 映射页面 -->
      <result>user_{1}.jsp</result>
    </action>
  </package>
</struts>
```

（5）返回结果的配置：

< result > 元素有两个属性，分别为`name`和`type`。

* name属性用于指定Result的逻辑名称，它与Action对象中方法的返回值相对应，如execute()方法返回值为input，name就将 < result > 元素的name属性配置为input，对应Action对象返回值。
* type属性用于设置返回结果的类型，请求转发重定向等。

# Struts2的开发模式

（一）实现与Servlet API的交互

Struts2中提供了Map类型的request、session与application，可以从ApplicationContext对象中获得。ApplicationContext对象位于com.opensymphony.xwork2包中。

* 实例化ActionContext
使用ActionContext对象提供的getContext()方法进行创建.getContext()方法是一个静态方法，可以直接调用，它的返回值是ActionContext。

* 获取Map类型的request
使用ActionContext对象提供的get()方法进行获取，get()方法的入口参数为Object类型的值，获取request可以将此值设置为request

```java
Map request = ActionContext.getContext.get("request");
```

* 获取Map类型的session
ActionContext类的getSession()方法返回Map对象，这个Map对象的范围作用于HttpSession范围中

* 获取Map类型的application
getApplication()方法返回的是Map对象，范围作用于ServletContext范围中

（二）域模型DomainModel

* 将某一领域的实体封装成一个实体对象，例如将用户信息封装成为一个领域对象User

```java
package com.zj.action;

import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionSupport;

/**
 * 用户 Action
 *
 * @author zhangjuan
 *
 */

public class UserAction extends ActionSupport {
  private static final long serialVersionUID = 1L;

  private User user;

  public User getUser() {
    return this.user;
  }

  public void setUser(final User user) {
    this.user = user;
  }

  @Override
  public String execute() {
    return Action.SUCCESS;
  }
  
}
```

* 在页面中提交注册请求的代码：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="s" uri="/struts-tags" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>userAdd</title>
</head>
<body>
	<h2>用户注册</h2>
	<s:form action="userAction" method="post">
		<s:textfield name="user.name" label="username"></s:textfield>
		<s:password name="user.password" label="password"></s:password>
		<s:radio name="user.sex" list="#{1:'M',0:'F'}" label="sex"></s:radio>
		<s:submit value="注册"></s:submit>
	</s:form>
</body>
</html>
```

（三）驱动模型ModelDriven

不需要指定请求参数所属的对象引用，就可以向实体对象中注入参数值。

表单代码：

```jsp
<s:form action="success.jsp" method="post">
    <s:textfield name="name" label="username"></s:textfield>
	<s:password name="password" label="password"></s:password>
	<s:radio name="sex" list="#{1:'M',0:'F'}" label="sex"></s:radio>
	<s:submit value="注册"></s:submit>
</s:form>
```

处理表单请求的UserAction对象需要实现ModelDriven接口，同时需要ModelDriven接口的getModel()方法，返回明确的实体对象user：

```java
package com.zj.action;

import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionSupport;
import com.opensymphony.xwork2.ModelDriven;

/**
 * 用户 Action
 *
 * @author zhangjuan
 *
 */

public class UserAction extends ActionSupport implements ModelDriven<User> {
  private static final long serialVersionUID = 1L;

  private User user;

  public User getUser() {
    return this.user;
  }

  public void setUser(final User user) {
    this.user = user;
  }

  @Override
  public String execute() {
    return Action.SUCCESS;
  }

  public User getModel() {
    // TODO Auto-generated method stub
    return this.user;
  }

}

```

（四）Struts2处理表单数据应用

（1）创建Java Web项目，将Struts的支持类型库文件添加到项目中，之后在web.xml文件中注册Struts2提供的过滤器。

（2）创建一个Action对象，用于对表单提交的信息（username）进行处理。
Struts2处理表单提交数据时，自动将表单中的属性注入到Action对象的成员变量中。
所以，在GreetingAction类中，提供一个String类型的变量username，此外，必须为username属性提供setUsername()方法：

```java
package com.zj.action;

import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionSupport;

/**
 * 用户 Action
 *
 * @author zhangjuan
 *
 */

public class GreetingAction extends ActionSupport {
  private static final long serialVersionUID = 1L;

  // 用户名
  private String username;

  @Override
  public String execute() {
    if (this.username == null || "".equals(this.username)) {
      return Action.ERROR;
    } else {
      return Action.SUCCESS;
    }
  }

  public String getUsername() {
    return this.username;
  }

  public void setUsername(final String username) {
    this.username = username;
  }

}
```

（3）在src/main/resources文件夹中创建名称为sturts.xml的配置文件。
Action请求的配置通过 < action > 元素进行定义，通过 < action > 元素的class属性指定GreetigAction类，name属性配置请求GreetingAction的地址，实例中配置为greeting，也就是说，当访问Web应用目录下的"/greeting"时，将由GreetingAction类对请求做出处理：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC  
    "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN"  
    "http://struts.apache.org/dtds/struts-2.1.dtd">
<struts>
  <!-- 声明包 -->
  <package name="myPackage" extends="struts-default" namespace="/jsp">
    <!-- 定义action -->
    <action name="greeting" class="com.zj.action.GreetingAction">
      <!-- 映射页面 -->
      <result name="success">success.jsp</result>
      <!-- 映射页面 -->
      <result name="error">error.jsp</result>
    </action>
  </package>
</struts>
```

（4）创建程序中的首页面index.jsp：

```jsp
<html>
<body>
	<form action="greeting.action" method="post">
	Input your name:<input type="text" name="username">
	<input type="submit" value="submit">
	</form>
</body>
</html>
```

（5）创建success.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="s" uri="/struts-tags"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>userUpdate</title>
</head>
<body>
	Hello! <s:property value="username"></s:property>
</body>
</html>
```

（6）创建error.jsp：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="s" uri="/struts-tags"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>userUpdate</title>
</head>
<body>
	Hello! <s:property value="username"></s:property>
</body>
</html>
```

# 使用Map类型的request、session和application：

（1）创建Java Web项目，将Struts的支持类型库文件添加到项目中，之后在web.xml文件中注册Struts2提供的过滤器。

（2）创建名称为TestAction的类：

```java
package com.zj.action;

import java.util.Map;

import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.ActionSupport;

/**
 * 用户 Action
 *
 * @author zhangjuan
 *
 */

public class TestAction extends ActionSupport {
  private static final long serialVersionUID = 1L;

  // Map类型的 request
  private final Map<String, Object> request;
  // Map类型的 session
  private final Map<String, Object> session;
  // Map类型的 application
  private final Map<String, Object> application;

  @SuppressWarnings("unchecked")
  public TestAction() {
    // 获取ActionContext对象
    final ActionContext context = ActionContext.getContext();
    // 获取Map类型的 request
    this.request = (Map<String, Object>) context.get("request");
    // 获取Map类型的 session
    this.session = context.getSession();
    // 获取Map类型的 application
    this.application = context.getApplication();
  }

  /**
   * 请求处理方法
   */
  @Override
  public String execute() throws Exception {
    final String info = "hello";
    this.request.put("info", info);
    this.session.put("info", info);
    this.application.put("info", info);
    return Action.SUCCESS;
  }
}
```

（3）struts.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC  
    "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN"  
    "http://struts.apache.org/dtds/struts-2.1.dtd">
<struts>
  <!-- 声明包 -->
  <package name="myPackage" extends="struts-default" namespace="/jsp">
    <!-- 定义action -->
    <action name="testAction" class="com.zj.action.TestAction">
      <!-- 映射页面 -->
      <result>success.jsp</result>
    </action>
  </package>
</struts>
```

（4）success.jsp：

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="s" uri="/struts-tags"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>userUpdate</title>
</head>
<body>
	request：
	<%=request.getAttribute("info") %>
	<br>
	session：
	<%=session.getAttribute("info") %>
	<br>
	application：
	<%=application.getAttribute("info") %>
	<br>
</body>
</html>
```

（5）index.jsp：

```jsp
<html>
<body>
	Map:
	<a href="testAction.action">request &nbsp; session &nbsp; application</a>
</body>
</html>
```





