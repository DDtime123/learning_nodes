# Spring Thymeleaf

**大纲：**

* Thymeleaf 介绍
* 将 Thymeleaf添加到项目中
* 创建 Web 控制器
* HTML 视图
* 模型 Model
* 使用博客模板
* 参考



##### 1. Thymeleaf 介绍

Thymeleaf 是一个前端模板引擎，为我们提供了在 HTML 页面中动态呈现内容的能力。它为 Web 应用的视图层提供HTML，XML，文本，JavaScript 或 CSS 文件，是 JSP 的替代品。

 ##### 2. 将 Thymeleaf添加到项目中

请在 pom.xml 中添加 Thymeleaf 的依赖包：

~~~xml
<dependency>
    <groupid>org.springframework.boot</groupid>
    <artifacfId>spring-boot-starter-thymeleaf</artifacfId>
</dependency>
~~~

##### 3. 创建 Web 控制器

SpringBoot 中控制器的作用是对客户端传来的请求作出响应，这里我们做出的响应是使用 Thymeleaf 将模板文件渲染成视图再展现到浏览器上。

仅仅有一个 Controller 组件 Spring Boot 应用就可以向请求返回一些消息：

~~~java
@Controller
public class HomeController {

    @GetMapping("/")
    public String home(){
        return "home";
    }
}
~~~

当 @Controller 作为注释添加到类上时，它会将请求 URL 映射到方法上，还会将请求重定向到 HTML 文件。

这里的 GetMapping 的方法 home() 仅仅响应了 HTTP 请求，而没有接收任何从 HTTP 请求上传递来的参数，其实有一种 @RequestParam 的变量级别注解可以让方法接受从 HTTP 请求上传来的参数。

~~~java
public String home(@RequestParam(name="http_name",Required=false) String name)
{
    // name can be used now
    ...
    return "home";
}
~~~

这个方法接受从 HTTP 请求上传递的名为 http_name 的字符串参数绑定到方法的 name 变量上，@Required=false 表明该属性是可选的，如果该属性的值为 true ，则必须要在 HTTP 请求处传递值，可以在 URL 后添加 ?http_name=hi 进行测试，这样 name 将被设置为 “hi”。

最后的 return "home" 实际上是个重定向操作，将 HTTP 请求重定向至 home.html 。

> 类级别和方法级别的 @RequestMapping("dir") 注释就是一个相对父子路径的关系。比如在类级别上添加了 @RequestMapping("dir") ，而在方法上添加了 @RequestMapping("home") ，那么访问该方法的访问路径就是 Host/dir/hello。



##### 4. HTML 视图

Controller 将 HTML 文件渲染成视图再传递会浏览器，在这期间，Thymeleaf 的作用就是解析 HTML 文件。

既然 Thymeleaf 这样的渲染 HTML 页面的框架会将 HTML 页面先解析一遍，那么显然它像其它的前端框架一样提供一套它的语法，或者说方言。

这里是 JSP 的标准方言：

~~~html
<form:inputText name="userName" value="${user.name}"/>
~~~

如无意外，它会去解析 user.name ，即使解析不成功，至少 HTML 的基本视图会被加载出来。

这里是 Thymeleaf 的标准方言：

~~~html
<input:inputText name="userName" value="Jamess Carrot" th:value="${user.name}"/>
~~~

这允许我们完成和以上相同的操作，并且为 value 提供一个默认值，即使 user.name 解析不成功，value 的值是确定的。



以下是一个 HTML 页面：

~~~html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Taco Cloud</title>
</head>
<body>
    <h1>Welcome to Thymeleaf</h1>
    <img style="width:200px;height:200px" th:src="@{/images/taco.jpg}"/>
    <h1 th:text="${name}"></h1>
</body>
</html>

~~~

th:* 属性声明了 thymeleaf 命名空间。

th:src 可以通过相对目录方式获取图片，相对根目录是 static，/images/taco.jpg 指的就是 src\main\resources\static\images\taco.jpg。



##### 5. 模型 Model

模型可以提供用于渲染视图的属性。

${name} 中的 name 是传递到 home 函数里的 Model 对象中的值，要想使用这个 name，要将在 home 的参数列表中添加 Model model，然后通过键值的方式将参数传递进 Model 里：

~~~java
@GetMapping("/hello")
public String home(@RequestParam(name="http_name",required=true) String name, Model model)
{
        model.addAttribute("name_in_html",name); // 可以通过 ${name_in_html} 获取 name 的值
        return "home";
}
~~~



##### 6. Thymeleaf 集成博客模板和 SpringBoot

这里有一个静态的博客模板，我们接下来要修改它，使用 Thymeleaf 模板并将其添加到 SpringBoot Web应用中： [博客模板](https://github.com/DDtime123/SpringBoot-web/blob/062e83ae130560fee21b45fcf75f67d6bcd15a6f/%E5%8D%9A%E5%AE%A2%E9%A3%8E%E6%A0%BCdemo%E7%BD%91%E7%AB%99.zip)

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014221241.png)

第一步，将静态模板配置好：

* 将 image 目录下文件放在 static/images 下
* 将 css 目录下的文件放在 static/css 下
* 将 3 个 HTML 文件放在 template 目录下

第二步，配置好后要在模板页面添加如下，这是在 SpringBoot 项目中添加模板文件的关键：

~~~html
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">
~~~

第三步，将模板的所有相对引用路径改为使用 thymeleaf 方言的相对路径，比如：

~~~html
src="./images/1.jpg" 改成
th:src="@{/images/1.jpg}"

href="./css/style.css" 改成
th:href="@{/css/style.css}"
~~~

第四步，配置好控制器，将 HTTP 请求重定向至 index.html ：

~~~java
@GetMapping("/Index")
public String index(){
    return "index";
}
~~~

最后就可以运行程序，查看执行结果了：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211014221754.png)





##### 7. 参考

[1] [Spring 中的 Model](https://www.baeldung.com/spring-mvc-model-model-map-model-view)

[2] [使用 Thymeleaf 开发 SpringBoot Web 应用](https://aanchalogy.medium.com/developing-cool-web-applications-in-spring-boot-by-using-thymeleaf-6658c0f73ebf)

[3] [在 Spring 中使用 Thymeleaf](https://www.baeldung.com/thymeleaf-in-spring-mvc)