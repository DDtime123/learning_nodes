# Spring Thymeleaf

**大纲：**

* Thymeleaf 介绍



#### 1. Thymeleaf

Thymeleaf 是一个前端模板引擎，为我们提供了在 HTML 页面中动态呈现内容的能力。它为 Web 应用的视图层提供HTML，XML，文本，JavaScript 或 CSS 文件，是 JSP 的替代品。

 ##### 1.1 将 Thymeleaf添加到项目中

请在 pom.xml 中添加 Thymeleaf 的依赖包：

~~~xml
<dependency>
    <groupid>org.springframework.boot</groupid>
    <artifacfId>spring-boot-starter-thymeleaf</artifacfId>
</dependency>
~~~

##### 1.2 创建 Web 控制器

SpringBoot 中控制器的作用是对客户端传来的请求作出响应，这里我们做出的响应是使用 Thymeleaf 将模板文件渲染成视图再展现到浏览器上。

~~~htm;

~~~

