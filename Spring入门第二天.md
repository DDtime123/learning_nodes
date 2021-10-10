### Spring入门第二天

在第一天中，我们创建了一个Spring MVC控制器处理浏览器请求，展现应用的主页，但是Spring MVC并不局限于展现静态内容。本篇将定制Taco的能力，深入研究Spring MVC展现模型数据和处理表单输入的能力。

本篇主要内容有：

* 在浏览器中展示模型数据

* 处理和校验表单数据

* 选择视图模板库

#### 1. 展现信息

> ​		Taco Cloud是一个可以在线订购的地方，除此以外，它还允许让用户根据自身的创意定制Taco。因此，Taco Cloud需要有一个页面为taco艺术家展现都可以选择哪些配料。可选的配料可能随时都会发生变化，所以不能把它们硬编码到HTML中，我们应当从数据库中获取可用的配料并将其传递给页面，进而展现给用户。

在Spring Web应用中，获取和处理数据是控制器的功能，将内容渲染到HTML中并展现到浏览器上是视图的任务。为了支撑Taco的创建页面，我们需要以下组件：

* 用来定义taco配料属性的领域类。
* 用来获取配料信息并将其传递值视图的Spring MVC控制器类。
* 用来在浏览器中渲染配料列表的视图模板。

组件关系如下：

![1632104145389](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632104145389.png)

本篇创建的控制器仅负责向视图提供配料，不与repository协作，不与数据库交互。

##### 1.1 构建领域类

> 应用的领域是指它所要解决的主题范围：也就是会影响到对应用的理解的理念和概念。在Taco Cloud应用中，领域对象包括Taco设计，组成Taco的配料，顾客和顾客所下的订单。首先关注Taco设计。

Taco配料是一种非常简单的对象，每种配料都有对应的名称和类型，以便对它们进行可视化的分类，每种配料还具有一个ID，这样对它们的引用就能十分容易和明确。如下的Ingredient类定义了我们所需的领域对象：

~~~java
package tacos;

import lombok.Data;
import lombok.RequiredArgsConstructor;


@Data
@RequiredArgsConstructor
public class Ingredient {

    private final String id;
    private final Type type;
    private final String name;

    public static enum Type{
        WRAP,PROTEIN,VEGGIES,CHEESE,SAUCE
    }
}
~~~

可以看见，这是一个非常普通的领域类，它定义了描述配料所需的3个属性，Ingredient类最不寻常的一点就是它似乎缺少了常见的getter和setter方法，以及equals，hashCode，toString等方法。

Ingredient 类中没有这些方法，原因之一是为了节省空间，此外我们引入了lombok类，这个类可以让我们在程序运行时动态生成这些方法。

类级别的@Data注解就是LomBok提供的，他会告诉Lombok所有缺失的方法，同时会生成所有以final属性作为参数的构造器。

##### 1.2 创建控制器类

在Spring MVC框架中，控制器负责处理HTTP请求。要么将请求传递给视图让其渲染成HTML，要么直接将数据写入响应体（RESTful）。本篇中将使用视图将请求渲染成HTML内容的方式。

Taco控制器需完成以下功能：

* 处理路径为 “/design” 的HTTP GET请求。
* 构建配料的列表。
* 处理请求，并将配料传递给要渲染的HTML视图模板，发送给发起请求的Web浏览器。

~~~java
// tacos/web/DesignTacoController.java
package tacos.web;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.validation.Valid;
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

import tacos.Taco;
import tacos.Ingredient;
import tacos.Ingredient.Type;

@Slf4j
@Controller
@RequestMapping("/design")
public class DesignTacoController {

    @GetMapping
    public String showDesignForm(Model model){
        List<Ingredient> ingredients = Arrays.asList(
                new Ingredient("FLTO","Flour Tortilla", Type.WRAP),
                new Ingredient("COTO","Corn Tortilla", Type.WRAP),
                new Ingredient("GRBF","Ground Beef", Type.PROTEIN),
                new Ingredient("CARN","Carnitas", Type.PROTEIN),
                new Ingredient("TMTO","Diced Tomatoes", Type.VEGGIES),
                new Ingredient("LETO","Lettuce", Type.VEGGIES),
                new Ingredient("CHED","Cheddar", Type.CHEESE),
                new Ingredient("JACK","Monterrey Jack", Type.CHEESE),
                new Ingredient("SLSA","Salsa", Type.SAUCE),
                new Ingredient("SRCR","Sour Cream",Type.SAUCE)
        );

        Type[] types = Ingredient.Type.values();
        for(Type type:types){
            model.addAttribute(type.toString().toLowerCase(),
                    filterByType(ingredients,type));
        }

        model.addAttribute("design",new Taco());

        return "design";
    }

    private List<Ingredient> filterByType(List<Ingredient> ingredients,Type type){
        return ingredients
                .stream()
                .filter(x -> x.getType().equals(type))
                .collect(Collectors.toList());
    }
}
~~~

~~~java
// tacos/Taco.java
package tacos;

import lombok.Data;

import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;
import java.util.List;

@Data
public class Taco {
    @NotNull
    @Size(min=5,message="Name must be at least 5 characters long")
    private String name;
    @Size(min=1,message = "You must choose at least 1 ingredient")
    private List<String> ingredients;
}
~~~

对于 DesignTacoController ，它所用的注解首先是@Slf4j，这是LomBok提供的注解，运行时它会在这个类中创建一个SLF4J（Simple Logging Facade for Java）Logger，这个简单的注解与下面通过代码显示声明的效果一致：

~~~java
private static final org.slf4j.Logger log = org.slf4j.LoggerFactory.getLogger(DesignTacoController.class);
~~~

DesignTacoController所用到的下一个注解是@Controller，这个注解会将这个类识别为控制器，并将其作为组件扫描的候选者。所以Spring会发现它并为它自动创建一个DesignTacoController实例，并将该实例作为Spring应用上下文中的bean。

@RequestMapping注解会让控制器处理对 “/design”地址发送的请求。

##### 1.3 处理GET请求

showDesignForm方法上的@GetMapping注解在类级别的@RequestMapping注解的基础上进行了细化，让控制器在接收到对 “/design” 的 HTTP GET 请求时，会调用showDesignForm方法。

@GetMapping是一个相对较新的注解，在Spring4.3引入，在这之前，需要使用方法级别的@RequestMapping注解作为替代：

~~~java
@RequestMapping(method = RequestMethod.GET)
~~~

但是@GetMapping更为简洁，并且指明了它的 HTTP 方法，以下为MVC中所有可用的请求映射注解：

![1632120864528](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632120864528.png)

现在看看showDesignForm方法做了什么工作。

这个方法构建了一个Ingredient对象的列表，现在这个列表是硬编码的，之后的篇章里会获取数据库中的taco配料并将其放入列表中。

配料列表准备就绪后，showDesignForm方法接下来几行代码会根据配料类型过滤列表，配料类型的列表会作为属性添加到Model对象上，这个对象是以参数形式传递到showDesignForm方法里的，Model对象负责在控制器和展现数据的视图之间传递数据。

放到Model属性中的数据会复制到Servlet Response的属性中，这样视图就能在这里找到它们。

showDesignForm最后返回 “design” ，这是视图的逻辑名称，会用来将模型渲染到视图上。

##### 1.4 设计视图

> Spring提供了多种视图构建方法，包括 JSP，Thymeleaf，FreeMarker，Mustache和基于Groovy的模板。这里会使用Thymeleaf。

> 像Thymeleaf这样的视图库在设计时是与特定的Web框架解耦的，因此它们无法感知Spring的模型抽象，也就无法与控制器放到Model中的数据协同工作。但是，它们可以与Servlet的request属性协作，所以，在Spring将请求转移到视图之前，它会把数据复制到request属性中，这样Thymeleaf和其他视图模板方案就能访问它们了。

Thymeleaf模板就是增加了一些额外元素的 HTML，这些属性能知道模板如何渲染 request 数据。例如，如果有一个请求属性的key为message，我们想要用 Thymeleaf 将其渲染到HTML <p> 标签中，在 Thymeleaf 模板中可以这样写：

~~~HTML
<p th:text="${message}">placeholder message</p>
~~~

当模板渲染成HTML时，<p> 元素将会被替换为Servlet Request 中Key 为 message 的属性值。th:text 时Thymeleaf命名空间中的属性，它会执行这个替换过程。${} 会告诉它要使用某个请求属性。

Thymeleaf还提供了一个属性 th:each ，它会迭代一个元素集合，为集合中的每个条目渲染 HTML。例如，如果想要渲染 wrap 配料的列表，可以用如下 HTML 片段：

~~~HTML
<h3>Designate your wrap:</h3>
<div th:each="ingredient : ${wrap}">
    <input name="ingredients" type="checkbox" th:value="${ingredient.id}">
    <span th:text="${ingredient.name}">INGREDIENT</span><br/>
</div>
~~~

这里，我们在 <div> 标签中使用 th:each 属性，这样就能针对 wrap Request属性对应的集合中的每个元素重复渲染 <div> 了。

taco艺术家用户会通过这个表单来提供其美味的作品，完整的 Thymeleaf 模板如下：

~~~html
<!-- res/templates/design.html -->
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Taco Cloud</title>
    <link rel="stylesheet" th:href="@{/styles.css}">
</head>

<body>
    <h1>Design your taco!</h1>
    <img th:src="@{/images/TacoCloud.png}">
    <form method="POST" th:object="${design}">

     <span class="validationError"
            th:if="${#fields.hasErrors('ingredients')}"
            th:error="*{ingredients}">Ingredient Error</span>

    <div class="grid">
        <div class="ingredient-group" id="wraps">
            <h3>Designate your wrap:</h3>
            <div th:each="ingredient:${wrap}">
                <input name="ingredients" type="checkbox" th:value="${ingredient.id}">
                <span th:text="${ingredient.name}">INGREDIENT</span>
            </div>
        </div>

        <div class="ingredient-group" id="proteins">
            <h3>Choose your protein:</h3>
            <div th:each="ingredient : ${protein}">
                <input name="ingredients" type="checkbox" th:value="${ingredient.id}">
                <span th:text="${ingredient.name}">INGREDIENT</span>
            </div>
        </div>

        <div class="ingredient-group" id="cheeses">
            <h3>Choose your cheese:</h3>
            <div th:each="ingredient : ${cheese}">
                <input name="ingredients" type="checkbox" th:value="${ingredient.id}">
                <span th:text="${ingredient.name}">INGREDIENT</span>
            </div>
        </div>

        <div class="ingredient-group" id="veggies">
            <h3>Choose your veggies:</h3>
            <div th:each="ingredient : ${veggies}">
                <input name="ingredients" type="checkbox" th:value="${ingredient.id}">
                <span th:text="${ingredient.name}">INGREDIENT</span>
            </div>
        </div>

        <div class="ingredient-group" id="sauces">
            <h3>Choose your sauce:</h3>
            <div th:each="ingredient : ${sauce}">
                <input name="ingredients" type="checkbox" th:value="${ingredient.id}">
                <span th:text="${ingredient.name}">INGREDIENT</span>
            </div>
        </div>


    <h3>Name your taco creation:</h3>
    <input type="text" th:field="*{name}">
    <br/>

    <button>Submit your taco</button>
    </div>
    </form>
</body>
</html>
~~~

这里引用的Taco 图片以及样式表都运用了Thymeleaf的 @{} 操作符，用来生成一个相对上下文的路径。静态内容要放在 /static 目录下。

启动浏览器，打开 localhost:8087/design，可以看到如下页面：

![1632127473129](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632127473129.png)

DesignTacoController还没有为接受创建Taco的请求做好准备，如果提交表单，用户会遇到一个错误（HTTP 405错误：Request Method "POST" Not Supported）。接下来，我们通过编写一些处理表单的控制器代码修正这个错误。

#### 2. 处理表单提交

在设计新的taco作品时，如果用户没有给他的taco设置作品名称时，或者当提交表单时没有填写所需的地址输入域时该如何是好呢？目前我们还没有指明这些输入域应该如何校验。

有一种校验方法是在Controller的processDesign和processOrder中加入大量的if else语句确保每个输入域满足确定的校验规则，但是这样的校验方式十分繁琐，且代码难以阅读。

如今Spring支出Java的Bean校验API，这样能更容易地声明校验。

在Spring MVC中使用校验，过程如下：

* 在被校验的类上声明校验规则：在这里是Taco类。
* 在控制器方法上声明要校验：就是DesignTacoController类的processDesign和processOrder方法上。
* 修改表单视图以展现校验错误。

Validation API提供了一些可以添加到领域对象上的注解以添加校验规则。

##### 2.1 声明校验规则





查看 form 标签，可以发现它的方法被设置成了 POST，除此以外，它并没有设置 action 属性，这意味着当表单提交时，浏览器将收集表单的所以数据，并以 HTTP POST请求的方式将其发送到服务器端，发送路径与渲染表单的GET请求路径相同，也就是 “/design”。

我们已经有了针对 “/design” 路径的类和对GET请求的处理方法，所以，我们应该在服务器端创建一个新的控制处理方法，用来对 “/design”地址发送的 POST 请求作出处理。

~~~java
// DesignTacoController.java
// DesignTacoController.processDesign方法
	@PostMapping
    public String processDesign(Taco design){
        log.info("Processing design: "+design);

        return "redirect:/orders/current";
    }
~~~

当表单提交的时候，表单中的输入域将绑定到Taco对象，该对象会以参数形式传递给processDesign，从这里开始，processDesign可以针对Taco采取任意操作。