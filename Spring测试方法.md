### Spring测试方法

> 在测试Web应用时，对HTML页面进行断言是比较困难的，幸好Spring提供了测试的方法。

#### 1. Spring测试

测试需要针对根路径  “/”  发送一个 HTTP GET 请求并期望得到成功结果。其中视图名称为 “home” 且结果内容包含 “Welcome to...”。

~~~java
package tacos;

import static org.hamcrest.core.StringContains.containsString;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.view;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

@RunWith(SpringRunner.class)
@WebMvcTest(HomeController.class)       // 针对HomeController进行测试
public class HomeControllerTests {
    @Autowired
    private MockMvc mockMvc;            // 注入MockMvc

    @Test
    public void testHomePage() throws Exception {
        mockMvc.perform(get("/"))
                .andExpect(status().isOk())
                .andExpect(view().name("home"))
                .andExpect(content().string(containsString("Welcome to...")));
    }
}
~~~

该测试没有使用SpringBootTest的注解，而是添加了@WebMvcTest注解。这是一个SpringBoot提供的特殊测试注解，它会让这个测试在SpringMVC应用的上下文里执行，也就是说，它会将HomeController注册到SpringMVC中，这样我们就可以向它发送请求了。

@WebMvcTest会为测试Spring MVC应用提供Spring环境，尽管可以启动一个服务器进行测试，但是对于该场景而言，仿造一下Spring MVC的运行机制即可。所以对测试类注入一个MockMvc，使得它能测试实现mockup。

通过testHomePage方法，首先使用MockMvc对象对根路径 “/” 发送了 “GET” 请求，对于这个请求，我们设置了如下预期：

* 相应应该具有 HTTP 200 (OK) 状态。
* 视图的逻辑名称应该是 “home”。
* 渲染后的视图应该包含文本 “Welcome to...”。

如果在MockMvc对象发送了这些请求后，不满足这些期望，则测试会失败。

### 总结

在构建Taco Cloud 应用过程中，执行了以下步骤：

* 使用Spring Initializr构建初始的项目结构。
* 编写控制类处理针对主页的请求。
* 定义了一个视图模板来渲染主页。
* 编写一个简单的测试类验证工作预期。

在一个Spring应用中，功能作用的代码占大多数，而为了满足框架需求的代码少之又少。在控制器中，除了开头的import语句外，就只能找到两行Spring相关的代码。而模板文件中更是一行Spring相关语句都没有。这就是使用Spring进行开发的一个优点：我们仅需要关注满足应用需求的代码而无需考虑如何满足框架的需求。

但Spring为了满足应用的需求，在幕后又做了什么呢？

在pom.xml 文件中，生命了对Web和Thymeleaf starter的依赖，这两项依赖会引入大量其它的依赖，包括：

* Spring的MVC框架
* 嵌入式的Tomcat
* Thymeleaf和Thymeleaf的布局方言

它还引入了SpringBoot的自动配置库，当应用启动时，SpringBoot的自动配置功能将会检测到这些库，并完成以下功能：

* 在Spring应用上下文中配置bean以启用Spring MVC。
* 在Spring应用上下文中配置嵌入式的Tomcat。
* 配置Thymeleaf视图解析器，使用Themeleaf模板渲染Spring MVC视图。









