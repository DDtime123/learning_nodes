### Spring创建REST风格API

#### 1. RESTful API概述

> 表征状态传输（也称为REST）是一种架构风格，应用程序由URI（统一资源定位符）唯一标识的资源。REST风格的应用程序客户端通过向资源映射的URI发送HTTP GET，POST，PUT和DELETE方法请求与资源进行交互。

> 如果Web服务遵循REST架构风格，则称为RESTful Web服务。在RESTful 服务的上下文中，可以将资源视为Web服务暴露的数据。客户端可以向REST Web服务器发送请求对暴露的数据进行CRUD（CREATE，READ，UPDATE，DELETE）操作。客户端和RESTful Web服务交换数据可以使用JSON，XML，格式或简单字符串，或HTTP支持的任何其他格式的MIME类型。

Restful风格由以下几个特点：

* 用URI描述资源。
* 使用HTTP方法描述行为，使用HTTP状态码表示不同的结果。
* 使用JSON交互数据。
* Restful只是一种风格，不是强制标准。

普通风格的请求方式如下表：

![1632192934176](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632192934176.png)

在普通方式中，可以看到增，删，改，查都是通过链接完成的，这时链接的主要功能就是进行操作。

Restful风格的请求方式如下表：

![1632192917433](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632192917433.png)

在Restful风格中，看不到query，create这种操作单词，这里的user指定的是资源，例如 /user/1 ，制定某一个条件是1的资源。那服务器如何知道对资源进行什么操作？这时，HTTP就起到描述行为的作用。

#### 2. 使用GET请求查询用户以及用户详情

最简单和常用的方式是使用GET请求，用于查询全部数据或者其中一条数据的详情，下面是Restful的GET请求。

##### 2.1 编写测试类程序

~~~java
// Restful/UserControllerTest.java
package tacos;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MockMvcBuilder;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

import java.awt.*;

@RunWith(SpringRunner.class)
@SpringBootTest
public class UserControllerTest {

    // 伪造测试用例，不需要执行Tomcat，运行速度会快一些
    @Autowired
    private WebApplicationContext wac;

    // 伪造一个MVC的环境
    private MockMvc mockMvc;

    @Before
    public void setUp(){
        mockMvc= MockMvcBuilders.webAppContextSetup(wac).build();
    }

    /**
     * @throws Exception
     * @Description 测试用例
     */
    @Test
    public void whenQuerySuccess() throws Exception{

        // 发送GET请求
        mockMvc.perform(MockMvcRequestBuilders.get("/user")
                .contentType(MediaType.APPLICATION_JSON_UTF8))
                .andExpect(MockMvcResultMatchers.status().isOk());
    }
}
~~~

最后测试应用程序，但是因为测试类还没写，HTTP正常返回状态是404，这是正常的，这是为了保证程序能够正常执行。

知识点：

* @RunWith注解：制定测试要运行的运行器，例如SpringRunner.class

* @Autowired注解：自动加载Bean

* MockMvc：MockMvc是服务端Spring MVC测试支持的主入口点，可以用来模拟服务端的请求。

* andExcept方法：进行预期匹配，例如MockMvcResultMatchers.status().isOk()表示预期匹配响应成功。

##### 2.2 常用注解及其测试

这里介绍Spring Boot的常用注解：

* @RestController：表明此Controller提供的是Restful服务。
* @RequestMapping及其变体：将URI映射到Java。
* @RequestParam：映射请求参数到Java上的参数。
* @PageableDefault：指定分页参数的默认值。

@RestController测试：

~~~java
// 控制器方法
@RestController
public class UserController {
    @RequestMapping(value="/user",method = RequestMethod.GET)
    public List<User> query(){
        List<User> userList=new ArrayList();
        userList.add(new User());
        userList.add(new User());
        userList.add(new User());
        userList.add(new User());
        return userList;
    }
}
~~~

~~~java
// 测试方法
    /**
     * @throws Exception
     * @Description 测试用例
     */
    @Test
    public void whenQuerySuccess() throws Exception{

        // 发送GET请求
        mockMvc.perform(MockMvcRequestBuilders.get("/user")
                .contentType(MediaType.APPLICATION_JSON_UTF8))
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andExpect(MockMvcResultMatchers.jsonPath("$.length()").value(4));
    }
~~~

在测试方法中，模拟了一个Restful服务，使用GET方法，访问的路径为 /user 。这个时候，程序会执行控制器中的query方法。



@RequestParam测试：

~~~java
// 控制器方法
    @RequestMapping(value="/user2",method = RequestMethod.GET)
    public List<User> query2(@RequestParam String username){

        // 打印观察是否收到username参数
        System.out.println("username is :"+username);
        List<User> userList=new ArrayList();
        // 将list中添加了三个空的user对象，这里暂时没给user进行属性赋值
        userList.add(new User());
        userList.add(new User());
        userList.add(new User());
        return userList;
    }
~~~

~~~java
// 测试方法
/**
     * @throws Exception
     * @Description 主要用于测试@RequestParam的单个参数接受
     */
    @Test
    public void whenQuerySuccess2() throws Exception{

        // 发送请求
        mockMvc.perform(MockMvcRequestBuilders.get("/user2")
                .param("username","tom")
                .contentType(MediaType.APPLICATION_JSON_UTF8))
                .andExpect(MockMvcResultMatchers.status().isOk());
    }
~~~

以上是一个接受单个参数的值得@RequestParam测试。

测试结果：

![1632203381750](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632203381750.png)

@RequestParam的其他属性：

![1632203469742](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632203469742.png)

defaultValue：参数的默认值。当存在这个参数时，如果访问的时候没有传递参数，程序中的参数会获取defaultValue中的值。

required：制定参数是否为必须传递的参数，默认是true。

name与value:name别名是value，所以name和value等价。在程序中，如果使用这个属性，就可以不在控制类的程序中使用访问时的参数，可以使用其别名。下面是控制类实例，测试参数别名：

~~~java
    @RequestMapping(value="/user3",method = RequestMethod.GET)
    public List<User> query3(@RequestParam(name="username") String myname){

        // 打印观察是否收到username参数
        System.out.println("username is :"+myname);
        List<User> userList=new ArrayList();
        // 将list中添加了三个空的user对象，这里暂时没给user进行属性赋值
        userList.add(new User());
        userList.add(new User());
        userList.add(new User());
        return userList;
    }
~~~

@RequestParam的不足：

如果需要传递多个参数，可否通过对象进行传递？控制器代码如下：

~~~java
// 控制器方法
	/**
     * @测试多参数传递
     * @return
     */
    @RequestMapping(value="/user4",method = RequestMethod.GET)
    public List<User> query4(UserQuery userQuery){

        // 通过反射的方式进行打印效果
        System.out.println(ReflectionToStringBuilder.toString(userQuery, ToStringStyle.MULTI_LINE_STYLE));
        System.out.println("username:"+userQuery.getUsername());
        List<User> userList=new ArrayList();
        // 将list中添加了三个空的user对象，这里暂时没给user进行属性赋值
        userList.add(new User());
        userList.add(new User());
        userList.add(new User());
        return userList;
    }
~~~

~~~java
// 测试方法
    /**
     * @throws Exception
     * @Description 主要用于测试@RequestParam的多参数传递
     */
    @Test
    public void whenQuerySuccess4() throws Exception{

        // 发送请求
        mockMvc.perform(MockMvcRequestBuilders.get("/user4")
                .param("username","tom")
                .param("age","18")
                .param("toAge","20")
                .contentType(MediaType.APPLICATION_JSON_UTF8))
                .andExpect(MockMvcResultMatchers.status().isOk());
    }
~~~

执行结果如图：

![1632204640491](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632204640491.png)

上面的程序中使用反射的方式输出了对象的属性。

##### 2.3 查询用户详情

@PathVariable：@PathVariable是映射URL片段到Java的注解。这里的注解也是获取URL中的参数，name它与@RequestParam注解有什么区别？

举例来说，使用@RequestParam注解时，URL是http://host:port/patj?参数名=参数值，这个注解获取的是这里的参数值；使用@PathVariable注解时，URL时候http://host:port/path/参数值，控制类代码如下：

~~~java
// 控制方法
    /**
     * @测试多参数传递
     * @return
     */
    @RequestMapping(value="/user/{id}",method = RequestMethod.GET)
    public User getInfo(@PathVariable(value="id") String idid){
        System.out.println("id is:"+idid);
        User user=new User();
        user.setUsername("tom");
        return user;
    }
~~~

~~~java
// 测试方法
    /**
     * @throws Exception
     */
    @Test
    public void whenGetInfoSuccess() throws Exception{

        // 发送请求
        mockMvc.perform(MockMvcRequestBuilders.get("/user/2")
                .contentType(MediaType.APPLICATION_JSON_UTF8))
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andExpect(MockMvcResultMatchers.jsonPath("$.username").value("tom"));
    }
~~~

控制台内容如下：

id is :2

通过控制类，通过@PathVariable注解可以获取路径中参数，这个参数时用户资源的一个查询条件，通过获取具体的条件就可以查询到具体的信息。上面的程序没有使用数据库连接操作数据，而是用静态方式模拟了这个效果。

#### 3. 使用POST处理创建请求

##### 3.1 @RequestBody注解

@RequestBody注解可以将请求体中的JSON字符串绑定到相应的Bean上，或者将其分别绑定到对应的字符串上。

控制方法代码如下：

~~~java
// 控制器方法
    @PostMapping("/user")
    public User createInfo(@RequestBody User user){
        System.out.println(user.getUsername());
        System.out.println(user.getPassword());
        user.setUsername("Bob");
        return user;
    }
~~~

~~~java
// 测试方法
    @Test
    public void whenCreateSuccess() throws Exception{
        String content="{\"username\":\"tom\",\"password\":\"123\"}";
        String result=mockMvc.perform(MockMvcRequestBuilders.post("/user")
                                .contentType(MediaType.APPLICATION_JSON_UTF8)
                                .content(content))
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andReturn().getResponse().getContentAsString();
        System.out.println("result: "+result);
    }
~~~

程序使用了@PostMapping注解，该注解是@RequestMapping的变体。

执行结果：

![1632209546249](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632209546249.png)

从执行结果可以看出，content中的Json值通过@RequestBody映射到user的Bean了，所以在控制类中可以通过对象得到属性值，同样可以通过SET对属性进行修改，返回。这里的result值就被修改且返回。

##### 3.2 @Valid注解

在开发业务时，需要满足校验，然后才会进行后台服务处理。校验的程序第一步，对校验字段进行校验要求；第二步，在服务上添加@Valid注解。

往字段上添加校验代码如下：

~~~java
public class User {

    /**
     *创建两个接口，一个为用户简单视图，即精简版的Json数据视图，另一个为用户详细视图，即完整版的Json数据视图
     */
    public interface UserSimpleView{};
    public interface UserDetailView extends UserSimpleView{};

    @NotBlank
    private String username;
    private String password;
    private String id;
    
    ...
}
~~~

然后再控制类中添加校验：

~~~java
// 控制类代码
@PostMapping("/user2")
    public User createInfo2(@Valid  @RequestBody User user){
        System.out.println(user.getUsername());
        System.out.println(user.getPassword());
        user.setUsername("Bob");
        return user;
    }
~~~

~~~java
// 测试方法
    @Test
    public void whenCreateSuccess2() throws Exception{
        String content="{\"username\":null,\"password\":\"123\"}";
        String result=mockMvc.perform(MockMvcRequestBuilders.post("/user2")
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .content(content))
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andReturn().getResponse().getContentAsString();
        System.out.println("result: "+result);
    }
~~~

测试结果：

![1632210093311](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632210093311.png)

程序没有通过校验，所以程序没有通过校验类，不会进入后台服务。

##### 3.3 BindingResult验证参数合法性

在上文的@Valid注解中，可以发现程序没有通过校验时，不会进入后台服务，但在一些情况下需要进入后台进行一些操作，此时由BindingResult发挥它的作用。

控制类代码如下：

~~~java
// 控制器方法
    @PostMapping("/user3")
    public User createInfo3(@Valid  @RequestBody User user, BindingResult errors){
        if(errors.hasErrors()){
            errors.getAllErrors().stream().forEach(error -> System.out.println("error message: "+error.getDefaultMessage()));
        }
        System.out.println(user.getUsername());
        System.out.println(user.getPassword());
        user.setUsername("Bob");
        return user;
    }
~~~

~~~java
// 测试方法
    @Test
    public void whenCreateSuccess3() throws Exception{
        String content="{\"username\":null,\"password\":\"123\"}";
        String result=mockMvc.perform(MockMvcRequestBuilders.post("/user3")
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .content(content))
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andReturn().getResponse().getContentAsString();
        System.out.println("result: "+result);
    }
~~~

测试结果：

![1632210537945](C:\Users\Administrator\Desktop\PPT\1632210537945.png)

这里不仅通过了校验，而且程序进入了后台服务，将报错信息存入BindingResult对象。

#### 4. 使用PUT和DELETE进行用户信息更新和删除

##### 4.1 用户信息修改

对资源更新的请求方法是PUT，但POST也可以请求更新资源。二者区别在哪里？

创建操作可以使用POST，也可以使用PUT，区别在于POST是作用在一个集合资源上的（/articles），而PUT操作是作用在一个具体资源上的（/articles/123）。如果URL可以在客户端确认，则使用PUT；如果实在服务端确认，则使用POST。比如很多资源使用数据库自增主键作为标识信息，而创建的资源的标识信息只能由服务端提供，这时候必须要用POST。

控制器方法如下：

~~~java
// 控制器方法
    @PutMapping("/user/{id}")
    public User putInfo4(@Valid  @RequestBody User user, BindingResult errors){
        if(errors.hasErrors()){
            errors.getAllErrors().stream().forEach(error -> System.out.println("error message: "+error.getDefaultMessage()));
        }
        System.out.println(user.getUsername());
        System.out.println(user.getPassword());
        user.setUsername("Bob");
        return user;
    }
~~~

~~~java
// 测试方法
    @Test
    public void whenPutSuccess4() throws Exception{
        // JDK1.8的特性
        Date date=new Date(LocalDateTime.now().plusYears(1).
                atZone(ZoneId.systemDefault()).toInstant().toEpochMilli());
        System.out.println(date.getTime());
        String content="{\"id\":\"1\",\"username\":\"tom\",\"password\":null,\"birthday\":"+date.getTime()+"}";
        String result=mockMvc.perform(MockMvcRequestBuilders.put("/user/4")
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .content(content))
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andReturn().getResponse().getContentAsString();
        System.out.println("result: "+result);
    }
~~~

测试结果：

![1632213647425](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632213647425.png)

content中的Json值通过@RequestBody映射到user的Bean，在控制类中通过对象得到属性值，然后通过SET对属性进行修改，返回。这里的result值就被修改且返回。

##### 4.2 用户信息删除

使用HTTP中的DELETE删除一个资源，控制类代码如下：

~~~java
/**
     * @Description 测试使用HTTP DELETE方法进行删除用户信息。
     */
    @DeleteMapping("/{id:\\d+}")
    public void delete(@PathVariable String id){
        System.out.println("id="+id);
    }
~~~

~~~java
// 测试器方法
@Test
    public void whenDeleteSuccess() throws Exception{
        mockMvc.perform(MockMvcRequestBuilders.delete("/user/1")
                .contentType(MediaType.APPLICATION_JSON_UTF8))
                .andExpect(MockMvcResultMatchers.status().isOk());
    }
~~~

#### 5. 总结

在本篇中，我们学习了使用@PathVariable注释来从请求URL获取信息，使用@RequestBody将JSON值映射到Bean中。