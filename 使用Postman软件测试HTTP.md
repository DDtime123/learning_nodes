### 使用Postman软件测试HTTP

#### 1. Postman简介

开发人员在开发时调试网络程序或者网页时需要一些方法跟踪网页请求，postman就是这样一款不仅能够调试简单的css，html，脚本等基本网页信息，还能模拟发送几乎所有类型的HTTP请求的测试软件。

#### 2. 使用Postman进行HTTP测试

##### 2.1 GET方法

不接收参数的GET方法测试：

~~~java
// 响应方法
@RequestMapping(value="/user",method = RequestMethod.GET)
    public List<User> query(){
        List<User> userList=new ArrayList();
        userList.add(new User());
        userList.add(new User());
        userList.add(new User());
        userList.add(new User());
        return userList;
    }
~~~

向 /user 地址发送的HTTP GET请求会触发以上的响应方法，该响应方法无需参数，返回值是User的列表，User被解析成JSON格式并展现在body中。

![1632223830306](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632223830306.png)



接收单个参数的GET方法测试：

~~~java
// 响应方法
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



![1632224340478](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632224340478.png)

接收多个参数的GET方法：

~~~java
// 响应方法
@RequestMapping(value="/user4",method = RequestMethod.GET)
    public List<User> query4(UserQuery userQuery){

        // 通过反射的方式进行打印效果
        System.out.println(ReflectionToStringBuilder.toString(userQuery, ToStringStyle.MULTI_LINE_STYLE));
        System.out.println("username:"+userQuery.getUsername());
        System.out.println("username:"+userQuery.getAge());
        System.out.println("username:"+userQuery.getToAge());
        List<User> userList=new ArrayList();
        // 将list中添加了三个空的user对象，这里暂时没给user进行属性赋值
        userList.add(new User());
        userList.add(new User());
        userList.add(new User());
        return userList;
    }
~~~

多个参数的GET方法跟单个参数的GET方法相比，使用了对象作为参数的载体。

![1632226077729](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632226077729.png)

##### 2.2 POST方法

使用@RequestBody将请求体中的JSON字符串映射到Controller中的Bean上：

~~~java
// 响应方法
@PostMapping("/user")
    public User createInfo(@RequestBody User user){
        System.out.println(user.getUsername());
        System.out.println(user.getPassword());
        return user;
    }
~~~



![1632227274863](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632227274863.png)

##### 2.3 PUT方法

创建操作可以使用POST，也可以使用PUT，区别在于POST是作用在一个集合资源上的（/articles），而PUT操作是作用在一个具体资源上的（/articles/123）。如果URL可以在客户端确认，则使用PUT；如果实在服务端确认，则使用POST。比如很多资源使用数据库自增主键作为标识信息，而创建的资源的标识信息只能由服务端提供，这时候必须要用POST。

PUT方法创建请求URL的方式与POST方法大致相同。

~~~java
// 响应方法
@PutMapping("/user/{id}")
    public User putInfo(@Valid  @RequestBody User user, BindingResult errors){
        if(errors.hasErrors()){
            errors.getAllErrors().stream().forEach(error -> System.out.println("error message: "+error.getDefaultMessage()));
        }
        System.out.println(user.getUsername());
        System.out.println(user.getPassword());
        return user;
    }
~~~

![1632228722429](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632228722429.png)

##### 2.4 DELETE方法

~~~java
// 响应方法	
@Test
    public void whenDeleteSuccess() throws Exception{
        mockMvc.perform(MockMvcRequestBuilders.delete("/user/1")
                .contentType(MediaType.APPLICATION_JSON_UTF8))
                .andExpect(MockMvcResultMatchers.status().isOk());
    }
~~~

使用HTTP DELETE请求删除资源，只需要将对应的资源URL在请求URL中指明即可。

![1632228185020](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632228185020.png)

#### 3. 总结

本篇简要介绍了使用postman工具发送HTTP请求，对网络应用程序进行调试的方法。