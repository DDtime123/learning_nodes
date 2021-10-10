[TOC]

### 1. Spring概述

Spring是一个用于构建企业级Java应用的分层开源框架，具有强大的扩展，融合能力，善于将各种单层框架糅合在一起，统一，高效地构造出提供企业级服务的应用系统。

#### 1.1 Spring的优势

* 方便集成其他框架
* 大量采用面向对象的设计思想
* 采用面向接口的设计思想
* 提供了测试框架。

#### 1.2 前言

>  任何应用程序都是由许多组件组成的，每一个组件负责整个应用功能的一部分，这些组件需要与其他元素协调以完成自己的任务，在应用程序运行时，需要以某种方式创建并引入这些组件。

Spring的核心提供了一个容器（container），通常称为Spring应用上下文（Spring application content），Spring应用上下文负责创建和管理应用程序的组件。通常这些组件也可称为bean。Spring应用上下文通过基于依赖注入（dependency injection，DL）的方式将bean装配在一起。这时，组件不再创建和管理组件自身的生命周期，而完全交由容器创建和管理，并将其注入到需要它们的bean中。通常这是通过**构造器参数**和**属性访问方法**实现的。

#### 1.3 Spring自动装配的逻辑

**假设有两个组件需要处理，商品服务（用来提供基本商品信息）和库存服务（用来获取商品水平），而商品服务依赖于库存服务才能提供完整的商品信息**

![应用组件通过Spring应用上下文进行管理并实现相互注入](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631146864954.png)

​	图1.2 应用组件通过Spring应用上下文进行管理并实现相互注入



### 2. 创建第一个Spring应用

#### 2.2 创建Spring项目

点击File -> project

![创建项目](C:\Users\Administrator\Desktop\新建文件夹\创建项目.png)

选择Spring Initializr创建Spring项目目录结构，选择Project的jdk版本1.8

![创建Spring项目](C:\Users\Administrator\Desktop\新建文件夹\创建Spring项目.png)

因 **https://start.spring.io/**无法连接，转而选择阿里云的spring镜像  **http://start.aliyun.com** 

![换用阿里云Spring初始化网站](C:\Users\Administrator\Desktop\新建文件夹\换用阿里云Spring初始化网站.png)

选择依赖包DevTools，Spring Web，Thymeleaf

![选择依赖包](C:\Users\Administrator\Desktop\新建文件夹\选择依赖包.png)

用maven成功构建Spring项目的目录结构如下

* mvnw和mvnw.cmd：即使电脑上没有安装maven，也可以通过这两个脚本来构建项目
* pom.xml：maven的构建规范
* TacoCloudApplication.java文件：是项目的主类
* static文件夹：存放静态资源文件，如图片，样式表，JavaScript文件等
* templates文件夹：存放用于渲染到浏览器上的内容的模板文件
* application.properties文件：存放配置属性的文件
* TacoCloudApplicationTests.java文件：测试文件，用于确保Spring应用上下文能够被正确加载

![1631195289744](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631195289744.png)

#### 2.3 maven构建规范解析

![1631234762113](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631234762113.png)

* dependencies 声明了4个依赖，除了创建Spring项目时选择的Web，Spring Boot DevTools，Thymeleaf外还添加了一个test依赖，test依赖提供了很多的测试功能，无需主动勾选。

* Thymeleaf和web依赖的名称中均包含有starter这个单词，Spring Boot starter依赖的特点是它们本身不包含任何库代码，而是传递性的拉取所其他的库。

* 最后提供了一个Spring Boot插件，它提供一个maven goal，让我们可以使用maven来运行应用。

#### 2.4 spring主类分析

![1631373948342](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631373948342.png)

@SpringBootApplicarion注解表明这是一个配置类，同时该注解是一个组合注解

* @SpringBootConfiguration：表明该类是一个配置类，后期可以按需添加基于Java的配置框架
* @EnableAutoConfiguration：允许自动配置，让Spring自动配置它认为我们会用到的类
* @ComponentScan：启用组件扫描，这样可以通过@Compenent，@Controller，@Service这样的注解声明其他类，Spring会自动发现他们并将它们注册为Spring应用上下文中的组件。

#### 2.5 spring测试类分析

![1631374471611](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631374471611.png)

Spring initializr为我们提供一个测试类作为起步。

### 3. 编写Spring应用

该应用包含两个代码构件：

* 一个控制器类，用来响应主页相关的请求
* 一个视图模板，用来定义主页长什么样子

#### 3.1处理web请求

Spring自带一个Web框架SpringMVC，SpringMVC的核心是控制器类。控制器的作用是处理的请求，填充可选的数据模型并将请求传递给一个视图，一边生成返回给浏览器的HTML。

##### 3.1.1 创建HomeController类

![1631376068668](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631376068668.png)

* @Controller声明这是一个Controller组件，为了让Spring扫描到它并自动创建HomeController的实例到Spring应用上下文中。
* home()是一个简单的处理请求的方法，@GetMapping("/")这个注解表示会处理针对"/"发起的Get请求，并返回home值。

##### 3.1.2 创建渲染到浏览器的HTML视图

![1631376609671](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631376609671.png)

##### 3.1.3 编译程序

![1631440122469](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631440122469.png)

##### 3.1.4 运行程序

![1631440148182](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631440148182.png)

##### 3.1.5 访问网页 localhost:端口号

![1631440301225](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631440301225.png)

### 4.总结

本篇描述了Spring的基础概念，利用idea创建了一个Spring Web应用，使用Spring可以快速搭建项目，集成其他框架，简化编写应用程序的步骤。