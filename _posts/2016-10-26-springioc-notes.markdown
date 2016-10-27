---
date: 2016-10-26 17:15
status: draft
title: Spring核心之IoC
layout: post
---

Spring核心技术：IoC（控制反转）和AOP（面向切面编程）

* 掌握控制反转的概念
* 掌握Spring中依赖注入的两种方式
* 掌握Spring中bean的配置
* 掌握Spring的自动装配的配置
* 掌握Spring中两种关键的bean的作用域
* 了解Spring中bean的生命周期
* 掌握Spring对bean的特殊处理

# Spring配置

![spring]({{ site.url }}/images/spring/1.jpg)

pom.xml：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.zj.mvnbook</groupId>
  <artifactId>helloworld</artifactId>
  <packaging>war</packaging>
  <version>0.0.1-SNAPSHOT</version>
  <name>helloworld Maven Webapp</name>
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
    <!-- 添加Spring依赖 -->  
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-core</artifactId>  
        <version>3.1.1.RELEASE</version>  
    </dependency>  
      
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-beans</artifactId>  
        <version>3.1.1.RELEASE</version>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-context</artifactId>  
        <version>3.1.1.RELEASE</version>  
    </dependency>  
      
    <dependency>  
        <groupId>org.springframework</groupId>  
        <artifactId>spring-jdbc</artifactId>  
        <version>3.1.1.RELEASE</version>  
    </dependency>
  </dependencies>
  <build>
    <finalName>helloworld</finalName>
  </build>
</project>
```

![spring]({{ site.url }}/images/spring/2.jpg)    

# 使用BeanFactory管理bean

BeanFactory才用Java经典的工厂模式，通过从XML配置文件或属性文件（.properties）中读取JavaBean的定义，来实现JavaBean的创建、配置和管理。

装载bean(src/test/java)：

```java
final BeanFactory factory = new ClassPathXmlApplicationContext("applicationContext.xml"); //装载配置文件
final User user = (User) factory.getBean("user");    //获取bean
```

applicationContext.xml(src/main/resources)：

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN//EN" "http://www.springframework.org/dtd/spring-beans.dtd">
<beans>  
    <!-- 建立数据源 -->
    <bean id="user" class="com.zj.user.User">  
        <property name="name">
          <value>zj</value>
        </property>  
    </bean>    
</beans>  
```

在 < beans > 标签中通过 < bean > 标签定义JavaBean的名称和类型，在程序代码中利用BeanFactory的getBean()方法获取JavaBean的实例并且向上转为需要的接口类型，这样在容器中就开始了这个JavaBean的生命周期。

# ApplicationContext

ApplicationContext接口有3个实现类，可以实例化其中任何一个类来创建Spring的Application容器：

* ClassPathXmlApplicationContext类：
从当前类路径中检索配置文件并装载它来创建容器的实例：

```java
final ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
```

* FileSystemXmlApplicationContext类：
通过参数指定配置文件的位置检索配置文件并装载它来创建容器的实例，可以获取类路径之外的资源：

```java
final ApplicationContext context = new FileSystemXmlApplicationContext("/src/main/resources/applicationContext.xml");
```

* WebApplicationContext类：
Spring的Web应用容器，有两种方法可以在Servlet中使用：   
    - 在 Servlet的web.xml文件中配置Spring的`ContextLoaderlistener`监听器
    - 在web.xml配置文件中添加一个Servlet，定义使用Spring的org.springframework.web.context.ContextLoaderServlet类

# 依赖注入

* bean的配置
在配置文件中的 < beans > 元素中配置一个子元素 < bean >，Spring的反转控制机制会根据 < bean > 元素的具体配置来实例化bean实例。

```xml
<bean id="user" class="com.zj.user.User">
...
</bean>  
```

其中id属性为bean的名称，class属性为对应的类名，这样通过BeanFactory容器的getBean("test")方法就可以获取到该类的实例。

* Setter注入
配置文件中的 < property > 元素可以为JavaBean的Setter()方法传参，即通过Setter()法官法为属性赋值：

（1）创建用户的Javabean：(src/main/java)User.java：

```java
package com.zj.user;

public class User {
  private String name;
  private int age;

  public String getName() {
    return this.name;
  }

  public void setName(final String name) {
    this.name = name;
  }

  public int getAge() {
    return this.age;
  }

  public void setAge(final int age) {
    this.age = age;
  }

}
```

（2）在Spring的配置文件applicationContext.xml中配置该JavaBean：

```xml
<!-- User Bean -->
    <bean id="user" class="com.zj.user.User">  
        <property name="name">
          <value>zj</value>
        </property>
        <property name="age">
          <value>22</value>
        </property>
    </bean>    
```

*如果当JavaBean的某个属性是List集合类型或数组时，需要使用`<list>`标签为List集合类型或数组的每一个元素赋值。*

（3）创建测试类（src/test/java）com.zj.user.UserTest：

```java
package com.zj.user;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.FileSystemXmlApplicationContext;

public class UserTest {

  public void testSpring() {
    final ApplicationContext context = new FileSystemXmlApplicationContext("/src/main/resources/applicationContext.xml");
    final BeanFactory factory = context;
    final User user = (User) factory.getBean("user");
    System.out.println("用户名：" + user.getName());
    System.out.println("年龄：" + user.getAge());
  }

}
```

（4）运行程序：

![spring]({{ site.url }}/images/spring/3.jpg)

* 构造器注入
常被用于类的初始化操作。 < constryctor-arg > 是 < bean > 元素的子元素。通过 < constryctor-arg > 元素的 < value > 子元素可以为构造方法传参。

（1）在用户JavaBean（src/main/java/com.zj.user.User.java）中创建构造方法：

```java
package com.zj.user;

public class User {
  private final String name;
  private final int age;

  public User(final String name, final int age) {
    super();
    this.name = name;
    this.age = age;
  }

  public void printinfo() {
    System.out.println("用户名：" + this.name);
    System.out.println("年龄：" + this.age);
  }
}
```

（2）在Spring的配置文件（src/main/resources）applicationContext.xml中通过 < constructor-arg > 元素为JavaBean的属性赋值，标签的赋值顺序必须与构造方法中参数的顺序以及参数类型相同：

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN//EN" "http://www.springframework.org/dtd/spring-beans.dtd">
<beans>  
    <!-- User Bean -->
    <bean id="user" class="com.zj.user.User">  
        <constructor-arg>
          <value>zj</value>
        </constructor-arg>
        <constructor-arg>
          <value>22</value>
        </constructor-arg>
    </bean>    
</beans>  
```

< constructor-arg > 元素中的`index`属性和`type`属性解决参数顺序和参数类型问题：
1. index属性：用于指定构造方法的参数索引，指定当前 < constructor-arg > 标签构造方法的哪个参数赋值。
2. type属性：可以指定参数类型以确定要为构造方法的哪个参数赋值，当需要赋值的属性在构造方法中没有相同的类型时，可以使用这个参数。

```xml
<beans>
  <!-- User Bean -->
  <bean id="user" class="com.zj.user.User">
    <constructor-arg type="java.lang.String">
      <value>zj</value>
    </constructor-arg>
    <constructor-arg index="1">
      <value>22</value>
    </constructor-arg>
  </bean>
</beans>  
```

（3）创建测试类（src/test/java）com.zj.user.UserTest：

```java
package com.zj.user;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserTest {

  public void testSpring() {
    final ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    final BeanFactory factory = context;
    final User user = (User) factory.getBean("user");
    user.printinfo();
  }

}
```

* 引用其他的bean
通过配置文件使用`<ref>`元素引用其他JavaBean的实例对象：

# 自动装配

* 按bean名称装配
< bean > 元素的byname属性以属性名区分自动装配。在容器中寻找与JavaBean的属性名相同的JavaBean，并将其自动装配到JavaBean中。

（1）User对象（src/main/java/com.zj.user）：

```java
package com.zj.user;

public class User {
  private String name;
  private int age;

  public String getName() {
    return this.name;
  }

  public void setName(final String name) {
    this.name = name;
  }

  public int getAge() {
    return this.age;
  }

  public void setAge(final int age) {
    this.age = age;
  }

}
```

（2）定义bean（PrintInfo），将User对象注入到PrintInfo对象中，并在该bean中声明输出User对象属性信息的方法：

```java
package com.zj.user;

public class PrintInfo {
  private User user;

  public User getUser() {
    return this.user;
  }

  public void setUser(final User user) {
    this.user = user;
  }

  // 打印 User 对象中的属性
  public void printUser() {
    System.out.println("打印User属性");
    System.out.println("用户名：" + this.user.getName());
    System.out.println("年龄：" + this.user.getAge());

  }

}
```

（3）在Spring的配置文件(src/main/resources)applicationContext.xml中设置bean的自动装配，Spring将根据bean中的属性名称自动将User对象注入到指定的bean中：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN//EN" "http://www.springframework.org/dtd/spring-beans.dtd">
<beans>
  <bean name="user" class="com.zj.user.User">
    <property name="name">
      <value>zj</value>
    </property>
    <property name="age">
      <value>22</value>
    </property>
  </bean>
  <bean autowire="byName" id="printInfo" class="com.zj.user.PrintInfo" />
</beans>  
```

（4）创建测试类PrintInfoTest（src/test/java/com.zj.user）：

```java
package com.zj.user;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class PrintInfoTest {
  public void testSpring() {
    final ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    final BeanFactory factory = context;
    final PrintInfo user = (PrintInfo) factory.getBean("printInfo");
    user.printUser();
  }
}
```

* 按bean类型装配

```xml
<bean autowire="byType" id="printInfo" class="com.zj.user.PrintInfo" />
```

# < bean > 元素的属性及说明

![spring]({{ site.url }}/images/spring/4.jpg)

