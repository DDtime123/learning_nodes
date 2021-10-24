# Spring Boot Mybatis

[源代码下载](https://github.com/DDtime123/Spring-MyBatis-project)

**操作 Mybatis 步骤：**

* 从 XML 或 Java 构建SQLSessionFactory
* 从 SQLSessionFactory 中获取 SQLSession
* SQLSession 通过映射器 Mapper 与数据库进行交互



**简介：**

MyBatis 是一个开源的持久化框架，它简化了 Java 应用程序中数据库访问的实现。它提供对自定义 SQL，存储过程和不同类型的映射关系的支持。简单地说，它是 JDBC 和 Hibernate 的替代品。



#### 1. **引入Mybatis：**

要使用 Mybatis，可以将 mybatis-x.x.x.jar 包放置于类路径（classpath）中。

如果使用 Maven 构建项目，则在 pom.xml 中添加如下依赖：

~~~xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>3.4.4</version>
</dependency>
~~~

这里使用 mysql 作为数据库，在 pom.xml 中添加如下依赖：

~~~xml
<dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.14</version>
        </dependency>
        <dependency>
            <groupId >org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.0.0</version>
        </dependency>
~~~



#### 3.  创建 Java API

##### 3.1 SQL会话工厂 （SQLSessionFactory）：

SQLSessionFactory 是每个 MyBatis 应用程序的核心类，这个类通过使用加载配置 XML 文件的 SQLSessionFactoryBuilder 类的 builder 方法实例化：

~~~java
// 初始化 SQLSessionFactory 的初始化内容容器
String resource = "org/mybatis/example/BlogMapper.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
// 初始化 SQLSessionFactory ，通过 SQLSessionFactoryBuilder 的 build 方法
SQLSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
~~~

对于一个实体的访问，我们创建一个 Java 配置文件，这个配置文件包括数据源定义，事务管理器等详细信息以及定义实体之间关系的映射器列表等设置，这些设置一起用来构建 SQLSessionFactory 实例。

这个配置有 Java 配置方式和 XML 配置方式：

Java 配置方式：

~~~java
... // 定义 DRIVER, URL, USERNAME, PASSWORD
    
public static SqlSessionFactory buildsqlSessionFactory() {
    DataSource dataSource = new PooledDataSource(DRIVER, URL, USERNAME, PASSWORD);
    
    Environment environment = new Environment("Development", new JdbcTransactionFactory(), dataSource);
    
    Configuration configuration = new Configuration(environment);
    // ...
    
    configuration.addMapper(BlogMapper.class);
    
    SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
    return builder.build(configuration);
}
~~~

XML配置方式：

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
</configuration>
~~~

这两种方式都给我们创建了一个 SQLSessionFactory  实例。



##### 3.2 SQL会话（SQLSession ）：

SQLSession 包含执行数据库操作，获取映射器和管理事务的方法。它通过 SQLSessionFactory 类实例化。此类的示例不是线程安全的。

执行数据库操作后，应关闭会话。由于 SqlSession 实现了 AutoCloseable 接口，我们可以使用 try-with-resources 块从 SQLSessionFactory 获取 SQLSession：

~~~java
// automaticly close in java
try(SqlSession session = sqlSessionFactory.openSession()){
 // do work   
}
~~~



#### 4. 映射器 Mapper

映射器是将方法映射到相应 SQL 语句的 Java 接口。MyBatis 提供了定义数据库操作的注解。

Mapper 告诉 Spring 怎么查找定义的 SQL 语句资源：

Mapper 接口：

~~~java
public interface PersonMapper() {
    @Insert("SQL Insert语句") // 注解中使用 #{} 语法获取 person 中的属性值
    public Integer save(Person person);
    
    // ...
    
    @Select("SQL Select语句")
    @Results(value={ 
        @Result(property = "personId", column = "personId"),
        @Result(property = "name", column = "name"),
        @Result(property = "address", javaType = List.class, column = "personId", many = @Many(select = "getAddress"))
    })
    public Person getPersonById(Integer personId);
    
    // ...
}
~~~

XML Mapper：

~~~xml
<!-- 相对于类路径的资源引用 -->
<mappers>
    <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
    <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
    <mapper resource="org/mybatis/builder/postMapper.xml"/>
</mappers>

<!-- 使用完全限定资源定位符（URL） -->
<mappers>
    <mapper resource="file:///var/mappers/AuthorMapper.xml"/>
    <mapper resource="file:///var/mappers/AuthorMapper.xml"/>
    <mapper resource="file:///var/mappers/AuthorMapper.xml"/>
</mappers>

<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
    <mapper resource="org.mybatis.builder.AuthorMapper"/>
    <mapper resource="org.mybatis.builder.BlogMapper"/>
    <mapper resource="org.mybatis.builder.PostMapper"/>
</mappers>

~~~

这里我们使用了如下 Mapper：

~~~xml
<!-- org/mybatis/example/StudentMapper.xml -->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.StudentMapper">
    
  <select id="selectStudent" parameterType="int" resultType="com.example.springmybatis.model.Student">
    select * from Stuinfo where id = #{id}
  </select>
    
  <insert id="addStudent" parameterType="com.example.springmybatis.model.Student">
        INSERT INTO stuinfo(id,name,email,createDate,updateDate)
        VALUES(#{id},#{name},#{email},#{createDate},#{updateDate})
  </insert>
   
    
</mapper>
~~~

所有这些映射的 SQL 语句都驻留在名为 mapper 的元素中，该元素包含一个名为 ”namespace“ 的属性。

select 标签中，id="selectStudent" 声明了在外部程序如何找到这条 sql 语句，resultType 则将查询的结果映射到类上。

insert 标签中，parameterType="Student" 声明该条语句从外部程序获取的参数来源。

在这个 Mapper.xml 文件中，我们在查询标签 select 中使用了查询全部 select * 的查询，我们提供的是 ResultType ，提供的领域对象是 Student bean。Java bean 中的属性将对应查询结果中的列名。

> 我们这里提供 Student 使用了它的全限定名，可以通过设置类型别名将全限定名换成一个比较短的名称，这样方便输入和查看：
>
> <typeAlias type="com.example.springmybatis.model.Student" alias = "Student"/>

如果遇到 Java bean 的属性和数据表中的字段不相同的情况呢？这时候就要使用 ResultMap 进行结果映射：

~~~xml
<resultMap id="studentMap" type="com.example.springmybatis.model.Student">
        <result property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="email" column="email"/>
        <result property="createDate" column="createDate"/>
        <result property="updateDate" column="updateDate"/>
</resultMap>
~~~

接下来将查询标签 select 修改为按照 resultMap 进行结果映射：

~~~xml
<select id="selectStudent" parameterType="int" resultMap="studentMap">
    select * from Stuinfo where id = #{id}
</select>
~~~



#### 5. MyBatis 注解

@Insert，@Select，@Update，@Delete注解代表通过调用注解的方式执行 SQL 语句：

~~~java
@Insert("Insert into table(attribute) values #{attribute_inContext}")
public Integer save(Person person);

@Update("Update table set name=#{attribute1_inContext} where attribure=#{attribute2_inContext}")
public void updatePerson(Person person);

@Delete("Delete from table where attribute=#{attribute_inContext}")
public void deleteByAttribute(Integer attribute);

@Select("SELETE table.attribute1,table.attribute2,table.attribute3 FROM table WHERE table.attribute4=#{attribute_inContext}")
public Person getPersonById(Integer personId);
~~~

注解中使用 #{} 语法获取上下文对象中的属性值。

@Results：一个结果集映射列表，其中包含有关如何将数据库列映射到 Java 类属性的详细信息：

~~~java
@Select("Select personId,name from Person where personId=#{personId}")
@Results(value={
    @Result(property="personId",column="personId"),
    //...
})
public Person getPersonById(Integer personId){}
~~~

@Result：它代表从 @Results 检索的结果列表中的单个 Result 实例。包括从数据库列到 Java bean 属性的映射，属性的 Java 类型，以及与其他 Java 对象的关联等细节：

~~~java
@Results(value={
    @Result(property="personId",column="personId"),
    @Result(property="name",column="name"),
    @Result(property="address",javaType=List.class)
    //...
})
public Person getPersonById(Integer personId){}
~~~

@Many：它指定一个对象到其它对象集合的映射：

~~~java
@Results(value={
    @Result(property="addresses",javaType=List.class,
           column="personId",
           many=@Many(select = "getAddress"))
})
~~~

这里的 getAddress 是通过查询 address 表返回地址集合的方法：

~~~java
@Select("select addressId, streetAddress, personId from address where personId = #{personId}")
public Address getAddress(Integer personId);
~~~

@MapKey：这用于将记录转换为具有由 value 属性定义的键的记录映射：

~~~java
@Select("select * from Person")
@MapKey("personId")
Map<Integer, Person> getAllPerson();
~~~



#### 6. Postman 测试

这里使用 Postman 对集成了 Mybatis 的 SpringBoot 程序进行测试。

update：

~~~xml
URL：http://localhost:8088/public/updateStu
请求方式：POST
请求正文格式：JSON
请求正文：
{
    "id" : "2",
    "name" : "Tomi",
    "email" : "123@sina.com"
}
~~~

请求结果：成功

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211022214819.png)

Insert：

~~~xml
URL：http://localhost:8088/public/addStu
请求方式：POST
请求正文格式：JSON
请求正文：
{
    "id" : "10",
    "name" : "Tony",
    "email" : "123@sina.com"
}
~~~

请求结果：成功

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211022220743.png)

select：

~~~xml
URL：http://localhost:8088/public/findStus?id=2
请求方式：GET
请求正文格式：无
请求正文：无
~~~

请求结果：成功

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211022220709.png)

delete：

~~~xml
URL：http://localhost:8088/public/delStus?id=2
请求方式：POST
请求正文格式：无
请求正文：无
~~~

请求结果：成功

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211022220256.png)

#### 7. 参考

[1] [Mybatis 快速指南](https://www.baeldung.com/mybatis)

[2] [MyBatis 入门](https://mybatis.org/mybatis-3/zh/getting-started.html)

[3] [MyBatis 教程](https://www.tutorialspoint.com/mybatis/mybatis_configuration_xml.htm)