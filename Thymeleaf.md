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



#### 3. Thymeleaf 语法

一个 Product 类的 bean 已经被加入上下文模型中：

~~~java
package org.thymeleaf.itutorial.beans;

import java.util.Date;

public class Product {

    private String description;
    private Integer price;
    private Date availableFrom;
    
    public Product(final String description, final Integer price, final Date availableFrom) {
        this.description = description;
        this.price = price;
        this.availableFrom = availableFrom;
    }

    public Date getAvailableFrom() {
        return this.availableFrom;
    }

    public void setAvailableFrom(final Date availableFrom) {
        this.availableFrom = availableFrom;
    }

    public String getDescription() {
        return this.description;
    }

    public void setDescription(final String description) {
        this.description = description;
    }

    public Integer getPrice() {
        return this.price;
    }

    public void setPrice(final Integer price) {
        this.price = price;
    }
    
}
~~~

变量语法：

~~~html
th:text="${product.属性}"
~~~

简单格式化：

~~~html
"${#dates.format(product.属性, "dd/MMMM/yyyy hh:mm:ss")}" # 日期格式化
"${#numbers.formatDecimal(product.属性, 3, 2)}" # 数值格式化
~~~

字符串连接：

~~~html
" '字符串' + ${product.属性} "
~~~

转义和未转义的文本：

如果我们的消息里有 标签 这种会被转义的文本：

~~~html
home.welcome=Welcome to our <b>fantastic</b> grocery store!
~~~

它会被转义成为：

~~~html
Welcome to our &lt;b&gt;fantastic&lt;/b&gt; grocery store!
~~~

迭代：

~~~html
<tr th:each="prod : ${productList}">
	<td th:text="${prod.description}">XXX</td>
	<td th:text="${prod.price}">XXX</td>
	<td th:text="${prod.availableFrom}">XXX</td>
</tr>
~~~

保持迭代状态：

Thymeleaf 的迭代过程中，使用一些状态变量用于跟踪迭代

index：索引状态变量，下标从 0 开始

count：迭代次数状态变量，从 1 开始计数

size：迭代变量中的元素总数

current：每次迭代的 iter 变量（th:each="每次iter变量 : 迭代变量"）

even/odd：当前迭代是奇数还是偶数

first：当前迭代是否为第一个

last：当前迭代是否为最后一次

用法：

~~~html
<tr th:each="prod,iterStat : ${prods}" th:class="${iterStat.odd}? 'odd'">
    <td th:text="${prod.name}">Options</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
</tr>
~~~

迭代，并且如果是奇数次迭代，就给生成的 tr 标签对象添加 odd 这个类，这是生成的源代码：

~~~html
...
<tr class="odd"> # 奇数次迭代，添加了 odd 类
	<td>1</td>
	<td>Wooden wardrobe with glass doors</td>
	<td>$850.00</td>
	<td>18-Feb-2013</td>
</tr>
<tr>
	<td>2</td>
	<td>Wooden wardrobe with glass doors</td>
	<td>$850.00</td>
	<td>18-Feb-2013</td>
</tr>
...
~~~

条件 if 和 unless：

条件的功能是：如果满足特定条件，则将模板的一个片段展现在视图中；或者

~~~html
<a href="comments.html"
	th:href="@{/product/comments(prodId=${prod.id})}"
   th:if="${not #lists.isEmpty(prod.comments)}"</a>
~~~

这里 #lists 使用的是格式化时的语法，# 井号用来引用表达式对象：

表达式对象包括基本对象和实用程序对象：

主要的基本对象：

* #ctx：上下文对象
* #vars：上下文变量
* #locale：上下文语言环境
* #httpServletRequest：（仅在 Web 上下文中）HttpServletRequest 对象
* #httpSession：（仅在 Web 上下文中）HttpSession 对象

主要的实用程序对象：

* #dates：java.util.Date 对象的使用方法：格式化，组件提取等。
* #calendars：java.util.Calendars 对象的方法
* #numbers：格式化数字的对象
* #strings：String 对象的方法：contains，startsWith等
* #arrays：数组的方法
* #lists：列表的方法
* #set：集合的方法

这段代码会在 if 条件成立时给生成的视图添加一条链接，即当产品有评论时创建链接到评论页面。

比较运算符：

不能使用 > 和 < 号：

~~~html
&lt; # 表示 < 小于
&gt; # 表示 > 大于
th:if="${product.price} &lt; 100"
~~~



switch 选择语句：

~~~html
<div th:switch="${user.role}">
    <p th:case="'admin'">User is an administrator</p>
    <p th:case="#{roles.manager}">User is a manager</p>
</div>
~~~





