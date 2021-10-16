# Thymeleaf

**大纲：**

* 从 HTML 到 HTML
* 杂货店网站



#### 1. 从HTML 到 HTML

##### 1.1 DOM 文档对象模型和 tag soup 标签汤

在最初网页刚刚形成的时候，HTML 仅定义了文档格式规则，后来通过 JavaScript 的使用给网页增加了交互性。这种交互性是通过脚本处理，修改甚至是执行正在显示的文档的事件来实现的，所以为了正确地将文档中的每个对象给标识出来，浏览器必须将 HTML 文档建模为内存中的对象树，给每个对象加上状态和事件，由此形成了文档对象模型（DOM）。

HTML 本就是一种格式宽松的语言，无严格的层次结构，而标签汤更是为 HTML 提供一种自动更正错误的机制使得格式不正确的 HTML 也能够被解析。



#### 2. 杂货店网站



![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211015194531.png)

Order 依赖 OrderLine 依赖 Product 依赖 Comment，Order 同时也依赖 Customer，OrderLine 与 Order 聚合。



类之间的关系表达：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211015200043.png)



这里使用 Thymeleaf 官方网站提供的示例项目：[Thymeleaf示例项目](https://github.com/thymeleaf/thymeleafexamples-gtvg)

根据示例项目的 README 文档指示，我们执行以下命令以运行项目：

~~~makefile
mvn clean
mvn compile
mvn tomcat7:run
访问 http://localhost:8080/gtvg
~~~

这里出现了一些问题，从 Github 下载下来的项目中的配置文件指明了 maven 执行编译命令 mvn compile 时使用的 java 版本是 6 ：

~~~xml
<properties>
    <java.version>6</java.version>
    ...
  </properties>
~~~

我们要将它改正为 8 版本，意为 java 1.8 ，当然这个根据各位的本机配置各异：

~~~xml
<properties>
    <java.version>8</java.version>
    ...
  </properties>
~~~



