# Java JDBC

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
	createdate DATETIME NOT NULL,
	updatedate DATETIME NOT NULL
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
package com.jdbc;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

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
                retMsg.setStatusMsg("Exception occurred.")
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
    public ResponseEntity<List<StuModel>> getStus(
            @RequestParam("make")
            String make,
            @RequestParam("startYear")
            int startYear,
            @RequestParam("endYear")
            int endYear)
    {
        List<StuModel> foundStus = stuRepo.findStu(make,startYear,endYear);

        if(foundStus == null){
            foundStus = new ArrayList<StuModel>();
        }

        ResponseEntity<List<StuModel>> retVal;
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

~~~