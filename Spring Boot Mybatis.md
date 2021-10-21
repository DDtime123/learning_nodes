# Spring Boot Mybatis

**大纲：**



**简介：**

MyBatis 是一个开源的持久化框架，它简化了 Java 应用程序中数据库访问的实现。它提供对自定义 SQL，存储过程和不同类型的映射关系的支持。简单地说，它是 JDBC 和 Hibernate 的替代品。



**配置Mybatis：**

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
String resource = "mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
// 初始化 SQLSessionFactory ，通过 SQLSessionFactoryBuilder 的 build 方法
SQLSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
~~~

对于一个实体的访问，我们创建一个 Java 配置文件，这个配置文件包括数据源定义，事务管理器等详细信息以及定义实体之间关系的映射器列表等设置，这些设置一起用来构建 SQLSessionFactory 实例：

~~~java
public static SqlSessionFactory buildsqlSessionFactory() {
    DataSource dataSource = new PooledDataSource(DRIVER, URL, USERNAME, PASSWORD);
    
    Environment environment = new Environment("Development", new JdbcTransactionFactory(), dataSource);
    
    Configuration configuration = new Configuration(environment);
    // ...
    
    SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
    return builder.build(configuration);
}
~~~



##### 3.2 SQL会话（SQLSession ）：

SQLSession 包含执行数据库操作，获取映射器和管理事务的方法。它通过 SQLSessionFactory 类实例化。此类的示例不是线程安全的。

执行数据库操作后，应关闭会话。由于 SqlSession 实现了 AutoCloseable 接口，我们可以使用 try-with-resources 块：

~~~java
// automaticly close in java
try(SqlSession session = sqlSessionFactory.openSession()){
 // do work   
}
~~~

~~~python
# automaticly close in python
with open(r"path/file","flag") as myfile:
    # read file then automaticly close
~~~



#### 4. 映射器 Mapper

映射器是将方法映射到相应 SQL 语句的 Java 接口。MyBatis 提供了定义数据库操作的注解：

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



JdbcTemplate 提供 @Autowired 对象自动装配注入：

~~~java
@Autowired
private NamedParameterJdbcTemplate sqlDao;
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

@Results：一个结果集映射列表，其中包含有关如何

