# Java JDBC

[源代码](https://github.com/DDtime123/Java-JDBC-MySQL/tree/master)

在 Spring Boot Web 应用程序中使用 Spring jdbcTemplate

**大纲：**

* 设置数据库
* Maven POM 依赖
* 主要入口
* REST API 控制器
* JDBC 连接配置
* 存储库类
* 测试应用程序
* 总结
* 参考

#### 1. 设置数据库

这里使用一个数据库用户和一个表来设置一个 MySQL 数据库。

* 创建数据库用户和数据库
* 在新创建的数据库中创建表

创建数据库用户和数据库的SQL脚本：

~~~mysql
-- user.sql
CREATE DATABASE studb;
CREATE USER 'studbuser'@'localhost' IDENTIFIED BY '123test123';
GRANT ALL PRIVILEGES ON studb.* TO 'studbuser'@'localhost';
FLUSH PRIVILEGES;
~~~

创建表的数据库：

~~~mysql
-- tables.sql

use studb;

DROP TABLE IF EXISTS stuinfo;

CREATE TABLE stuinfo(
	id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
	name varchar(64) NOT NULL,
	email varchar(64),
	createDate DATETIME NOT NULL,
	updateDate DATETIME NOT NULL
);
~~~



#### 2. Maven POM 依赖

接下来在 pom.xml 里配置项目的依赖包：

~~~xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.11</version>
        </dependency>
~~~

所需的依赖项有：

* spring-boot-starter-web：这是创建基于 Web 的应用程序所必需的，它支持 MVC 和 RESTFul 应用程序
* spring-boot-starter-jdbc：使用 Spring DAO JDBC 功能必需
* mysql-connector-java：提供特定于 MySQL 数据库的 jdbc 驱动程序所必需

当使用 Maven 进行构建时，它将创建一个 jar 包程序，可以使用 java 命令运行。



#### 3. 主要入口

一旦使用 Spring Initializer 创建 Spring Boot 项目，它就会创建一个类似如下的入口程序。

基于 Spring Boot 的 Web 应用程序不需要应用程序容器来承载它，它具有一个内嵌的 Tomcat 服务器，可以处理 Web 请求和 静态 Web 请求。由于无需程序容器，可以直接将 Web 应用程序打包成 jar 包，就可以作为普通 java 程序运行。

~~~java
package com.jdbc;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemojdbcApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemojdbcApplication.class, args);
    }

}
~~~

使用注解将该类标记为 Spring 启动应用程序。并且主入口方法会将该类传递给 SpringApplication.run()，Spring Boot 的应用程序运行器将创建 IOC 容器，并扫描所有包和子包以查找可添加到 IOC 容器中的类。



#### 4. REST API 控制器

~~~java
package com.jdbc.controller;

import com.jdbc.models.GenericResponse;
import com.jdbc.models.StuModel;
import com.jdbc.repository.StuRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.List;

@RestController
public class SampleController {
    @Autowired
    private StuRepository stuRepo;

    @RequestMapping(value="/public/addStu", method= RequestMethod.POST)
    public ResponseEntity<GenericResponse> addStu(
            @RequestBody
                    StuModel stuToAdd
    )
    {
        GenericResponse retMsg = new GenericResponse();
        if(stuToAdd != null)
        {
            try
            {
                stuRepo.addStu(stuToAdd);

                retMsg.setSuccess(true);
                retMsg.setStatusMsg("Operation is successful.");
            }
            catch(Exception ex)
            {
                ex.printStackTrace();
                retMsg.setSuccess(false);
                retMsg.setStatusMsg("Exception occurred.");
            }
        }
        else
        {
            retMsg.setSuccess(false);
            retMsg.setStatusMsg("No valid stu model object to be added");
        }

        ResponseEntity<GenericResponse> retVal;
        retVal = ResponseEntity.ok(retMsg);
        return retVal;
    }

    @RequestMapping(value="/public/getStus", method=RequestMethod.GET)
    public ResponseEntity<StuModel> getStus(
            @RequestParam("id")
            String id)
    {
        StuModel foundStus = stuRepo.findStu(id);

        if(foundStus == null){
            foundStus = new StuModel();
        }

        ResponseEntity<StuModel> retVal;
        retVal = ResponseEntity.ok(foundStus);
        return retVal;
    }


}
~~~

@RestController 注释能够让 Spring Boot 将其识别为 RESTFul API 控制器。



在类内部，有两种方法，用于处理用户的 HTTP 请求，分别是添加 stu 模型进数据库以及查询数据库的 stu 信息。这两种方法使用相同的方式访问和存储数据库信息，这就是 JdbcTemplate 对象。



如果要往数据库中添加对象，用如下方法：

~~~java
@Autowired
private StuRepository stuRepo;

...
stuRepo.addStu(stuToAdd);
~~~

要查询某个对象，使用如下方法：

~~~java
@RequestMapping(value="/public/getStus", method=RequestMethod.GET)
public ResponseEntity<List<StuModel>> getStus(
	@RequestParam("id")
    String id)
{
    List<Student> foundStus = stuRepo.findStu(id);
    
    if(foundStus == null){
        foundStus = new ArrayList<StuModel>();
    }
    
    ResponseEntity<List<StuModel>> retVal;
    retVal = ResponseEntity.ok(foundStus);
    return retVal;
}
~~~



#### 5. JDBC 连接配置

为了设计这样的数据访问数据库，需要进行配置，配置的主要内容有：

* 数据库连接字符串
* 用于连接数据库的用户名和密码
* 交易管理器
* 数据库jdbc驱动类

首先进行配置，为了适应 jdbcTemplate：

~~~java
package com.jdbc.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

import javax.sql.DataSource;

@Configuration
public class jdbcConfiguration {

    // 1. 数据源对象，带有配置参数
    @Bean
    public DataSource dataSource()
    {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();

        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost/studb?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC");
        dataSource.setUsername("studbuser");
        dataSource.setPassword("123test123");

        return dataSource;
    }

    // 2. 带有命名参数的 jdbcTemplate 对象，能够进行带有命名参数的查询
    @Bean
    public NamedParameterJdbcTemplate namedParameterJdbcTemplate()
    {
        NamedParameterJdbcTemplate retBean = new NamedParameterJdbcTemplate(dataSource());
        return retBean;
    }

    // 3. 事物管理器类
    @Bean
    public DataSourceTransactionManager txnManager()
    {
        DataSourceTransactionManager txnManager = new DataSourceTransactionManager(dataSource());
        return txnManager;
    }
}
~~~

Spring Boot 使用 @Configuration 注解将该类识别为配置类，配置类的使用时机是启动时：

~~~java
@Configuration
public class jdbcConfiguration{
    ...
}
~~~

dataSource() 方法返回 DriverManagerDataSource类的对象。该类使用MySQL jdbc 驱动程序进行配置用户名和密码等操作。

~~~java
@Bean
public DataSource dataSource()
{
    DriverManagerDataSource dataSource = new DriverManagerDataSource();
    
    dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
    dataSource.setUrl("jdbc:mysql://localhost:3306/studb");
    dataSource.setUsername("studbuser");
    dataSource.setPassword("123test123");
    
    return dataSource;
}
~~~

> 这也可以通过 XML 配置数据源来完成：
>
> ~~~xml
> <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource"
>       destroy-method="close">
>     <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
>     <property name="url" value="jdbc:mysql://localhost:3306/studb"/>
>     <property name="username" value="studbuser"/>
>     <property name="password" value="123test123"/>
> </bean>
> ~~~

##### 5.1 jdbcTemplate 对象

Spring JDBCTemplate 是一种强大的连接数据库和执行 SQL 查询的机制，它内部使用 JDBC API，但是消除了 JDBC API 的很多问题。

我们通常说 DAO 对象将执行 CRUD 操作，这里使用 NamedParameterJdbcTemplage 进行带有命名参数的查询，它是一个 jdbcTemplage 的特定类型的对象，我们也将使用它作为 DAO 对象：

~~~java
@Bean
public NamedParameterJdbcTemplate namedParameterJdbcTemplate()
{
    NamedParameterJdbcTemplate retBean = new NamedParameterJdbcTemplate(dataSource());
    return retBean;
}
~~~

> @Bean 注解告诉 Spring ，以 @Bean 注释的方法将返回应注册为 Spring 应用程序上下文的 bean 的对象。

> 命名参数 NamedParameterJdbcTemplate 包装了 JdbcTemplate 并且使用 ? 来指定参数，在幕后，它将命名参数替换为 JDBC ? 占位符并委托给包装的 JdbcTemplate 来执行查询。
>
> 一般的使用方法是：
>
> ~~~java
> SqlParameterSource namedParameters = new MapSqlParameterSource().addValue("id",1); // 使用 MapSqlParameterSource 为命名参数提供值
> return nameParameterJdbcTemplate.queryForObject("SQL 查询语句", nameParameters, String.class);
> ~~~

这个方法创建一个 NamedParameterJdbcTemplate 的对象，它需要一个数据源对象 dataSource。

最后需要一个事务管理器对象，Spring 的事务管理器可用于向存储库方法添加事务注释，以便 CRUD 操作可以包装到 SQL 事务中：

~~~java
@Bean
public DataSourceTransactioonManager txnManager()
{
    DataSourceTransactionManageer txnManager = new DataSourceTransactionManager(dataSource());
    return txnManager;
}
~~~



#### 6. 存储库类

DAO 对象能够进行 CRUD 操作，而这里我们使用一个 repository 对象用于与SQL数据库进行CURD操作。为简单起见，这里在 Controller 类中直接使用了 repository 对象，但是现实情况中应该将 DTO 对象和数据实体对象分开，在 repository 层之上再建立一层服务层。

~~~java
package com.jdbc.repository;

import com.jdbc.models.StuModel;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;

import java.util.*;

@Repository
public class StuRepository {
    // 1. JdbcTemplate 对象的自动装配注入
    @Autowired
    private NamedParameterJdbcTemplate sqlDao;

    private final String addStu_sql = "INSERT INTO stuinfo(id,name,email,createDate,updateDate)"
            + "VALUES(:id,:name,:email,:createDate,:updateDate)";

    private final String getStus_sql = "SELECT id,"
            + "name,"
            + "email,"
            + "createDate,"
            + "updateDate "
            + "FROM stuinfo WHERE id = :id";

    @Transactional
    public void addStu(StuModel stuToAdd)
    {
        if(stuToAdd != null)
        {
            Map<String, Object> parameters = new HashMap<String, Object>();

            Date dateNow = new Date();

            parameters.put("id", stuToAdd.getId());
            parameters.put("name",stuToAdd.getName());
            parameters.put("email", stuToAdd.getEmail());
            parameters.put("createDate", dateNow);
            parameters.put("updateDate", dateNow);

            int retVal = sqlDao.update(addStu_sql,parameters);
            System.out.println("Rows updated: "+retVal);
        }
        else
        {
            System.out.println("Car to add is invalid. Null Object.");
        }
    }

    @Transactional
    public StuModel findStu(String id){
        StuModel foundObjs = sqlDao.query(getStus_sql,
                (new MapSqlParameterSource())
                .addValue("id",id),
                (rs) -> {
            StuModel retVal = new StuModel();
            if(rs != null)
            {
                while(rs.next())
                {
                        StuModel sm = new StuModel();
                        sm.setCreateDate(rs.getDate("createDate"));
                        sm.setUpdateDate(rs.getDate("updateDate"));
                        retVal = sm;
                }
            }

            return retVal;
                });

        return foundObjs;
    }
}
~~~

在 repository 类里，第一件事就是 声明对象自动装配注入，这个 DAO 对象将用于数据访问 CRUD 操作：

~~~java
@Autowired
private NamedParameterJdbcTemplate sqlDao;
~~~

接下来是两个查询字符串 addStu_sql 和 getStus_sql，用于查询。

这里使用了命名参数，语法是 : id, :email 

~~~java
private final String addStu_sql = "INSERT INTO stuinfo(id,name,email,createDate,updateDate)"
            + "VALUES(:id,:name,:email,:createDate,:upDatedate)";
~~~

然后是添加方法的声明，

~~~java
@Transactional
public void addStu(StuModel stuToAdd)
{
	if(stuToAdd != null)
        {
            Map<String, Object> parameters = new HashMap<String, Object>();

            Date dateNow = new Date();

            parameters.put("id", stuToAdd.getId());
            parameters.put("name",stuToAdd.getName());
            parameters.put("email", stuToAdd.getEmail());
            parameters.put("createDate", dateNow);
            parameters.put("updateDate", dateNow);

            int retVal = sqlDao.update(addStu_sql,parameters);
            System.out.println("Rows updated: "+retVal);
        }
        else
        {
            System.out.println("Car to add is invalid. Null Object.");
        }  
}
~~~

repository 类的添加方法的工作方式是：

* 定义一个Map对象，其中键与命名参数匹配，值是将添加到数据库中的值。
* 使用 NamedParameterJdbcTemplate 对象 sqlDao 来调用update()，传入 addStu_sql
* 对 update 的调用将返回一个整数，表示有多少行记录受影响



然后是查询方法的声明：

~~~java
@Transactional
    public StuModel findStu(String id){
        StuModel foundObjs = sqlDao.query(getStus_sql,
                (new MapSqlParameterSource())
                .addValue("id",id),
                (rs) -> {
            StuModel retVal = new StuModel();
            if(rs != null)
            {
                while(rs.next())
                {
                        StuModel sm = new StuModel();
                    	sm.setId(String.valueOf(rs.getInt("id")));
                        sm.setName(rs.getString("name"));
                        sm.setEmail(rs.getString("email"));
                        sm.setCreateDate(rs.getDate("createDate"));
                        sm.setUpdateDate(rs.getDate("updateDate"));
                        retVal = sm;
                }
            }

            return retVal;
                });

        return foundObjs;
    }
~~~

这里的 NamedParameterTemplate 类的 query 方法传入三个参数：

* 要执行的 SQL 语句：getStu_sql，查询字符串

* 绑定到查询的参数容器：MapSqlParameterSource的对象，它是一个包装过的 Map 对象，键匹配命名参数，值是命名参数的值

  > 这里使用的参数 MapSqlParameterSource 类是用来将值绑定到跟数据库表中的字段名相同的键上的，根据不同的 query 方法的实现也可以使用 Map<String, ?> 对象来替代，比如使用 Collections.singletonMap("id",id) 替代第二个参数也是一个选择。

* 将提取结果的对象，返回任意的结果对象：这是个用 lambda 写的接口的实现，将 ResultSet 结果集映射到实体对象。

其功能是：查询给定的 SQL 以从 SQL 创建准备好的语句和要绑定到查询的参数列表，使用 ResultSetExtractor 读取 ResultSet。 

> NamedParameterTemplate 的 query 方法有多个实现方法，通过接受不同的参数以不同的方式提取结果。这里使用以下实现，返回任意的结果对象，
>
> ~~~java
> @Nullable 
> public <T> T query( String  sql,
>                               SqlParameterSource  paramSource,
>                               ResultSetExtractor <T> rse)
> ~~~



#### 7. 测试应用程序

第一步，构建应用程序：

~~~makefile
mvn clean install # 初次安装 
# mvn install # 修改项目后，再次编译
~~~

第二步，启动应用程序：

~~~java
java -jar target\demo-0.0.1-SNAPSHOT.jar
~~~

> 启动应用程序时，也许会遇到端口被占用的情况。Spring Web 应用程序有一个内嵌的 Tomcat 服务器，因为是 Spring 的属性，请在 Spring boot 配置文件 main\resources\application.properties 中添加属性以覆盖默认属性值：
>
> ~~~java
> server.port = 8081
> ~~~
>
> 参考：[如何在 Spring boot 中更改默认端口](https://www.baeldung.com/spring-boot-change-port)

> 错误勘误：MySQL JDBC 驱动程序自 5.133x版本后要和 UTC 时区一起工作，必须在连接字符串中声明：
>
> ~~~java
> jdbc:mysql://localhost/studb?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
> ~~~
>
> 参考：[MySQL JDBC 连接出现时区错误](https://stackoverflow.com/questions/26515700/mysql-jdbc-driver-5-1-33-time-zone-issue)



第三步：使用 Postman 测试应用程序：

> Postman 是一款不仅能够调试简单的 css，html，脚本等基本网页信息，还能模拟发送几乎所有类型的 HTTP 请求的测试软件。
>
> [使用 Postman模拟HTTP请求](https://zhuanlan.zhihu.com/p/412314578)

根据我们在 Controller 里设置的 HTTP 请求拦截，我们可以通过如下的 HTTP

请求，将 StuModel 对象添加到数据库中：

~~~http
http://localhost:8081/public/addStu
~~~

此 URL 仅接受 HTTP Post 请求，请求正文将是一个用于表示 StuModel 对象的 JSON 字符串：

~~~json
{
    "id" : "2",
    "name" : "michael",
    "email" : "142@sina.com"
}
~~~

完整的测试过程：

* 将 URL 设置为 http://localhost:8081/public/addStu；
* 将请求类型设置为 POST；
* 在请求正文部分包含表示 StuModel 对象的 JSON 字符串；
* 将内容类型设置为 JSON

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211012140755.png)

点击 Send，服务器返回成功响应：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211012141212.png)



我们通过以下的带有参数的 HTTP GET 请求，查找 Stu：

~~~http
http://localhost:8081/public/getStus?id=3
~~~

在 Postman 软件中，在 Params 中添加参数：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211012221229.png)

点击 Send，服务器返回查找结果：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211012221328.png)



#### 8. 总结

本篇里使用了 Spring JDBCTemplate 在 Spring Boot Web 应用中获取 MySQL 数据，且集成了测试以及 RESTFul API 的创建。



#### 9. 参考

[1] [Spring Jdbc 模板](https://www.baeldung.com/spring-jdbc-jdbctemplate)

[2] [在 Spring Boot Web 应用程序中使用 jdbcTemplate](https://www.codeproject.com/Articles/1269020/Using-JdbcTemplate-in-a-Spring-Boot-Web-Applicatio)