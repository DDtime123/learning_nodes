# Spring 自动配置注入

JdbcTemplate 提供 @Autowired 对象自动装配注入：

~~~java
@Autowired
private NamedParameterJdbcTemplate sqlDao;
~~~

@Autowired 让 Spring 解析 bean 并将 bean 注入我们的 bean 中。在 Spring 配置文件中声明所有依赖项，Spring IOC 容器可以自动装配协作 bean 之间的关系，这就是 Spring bean 的自动装配。



#### 1. 在 Java 中定义 bean 和 在 XML 中定义 bean ，并将其使用在 Spring 自动配置注入中

ref： [使用 @Configuration 的 Spring Java 配置](https://www.netjstech.com/2015/10/spring-example-program-using-javaconfig-annotations.html)

为了在 Spring 中使用依赖注入，我们得使用配置，这里有基于 Java 和注解的配置和基于 XML 的配置：

使用基于 Java 注解 的配置，要使用 @Configuration 注解。

一个应用程序可以使用一个或多个带有 @Configuration 注释的类。@Configuration 相当于 XML 的 ”beans“ 元素，这两个都为所有封闭的 bean 提供了显式定义默认值的机会：

~~~java
@Configuration(defaultAutowird=Autowire.BY_TYPE,defaultLazy=Lazy.FALSE)
~~~

@Configuration 表明该类具有 @Bean 定义方法，而用 @Bean 注释方法表明该方法将返回能够在 Spring IOC 容器（也称为 Spring 应用上下文）中注册为组件（或者成为bean）的 bean ：

~~~java
@Configuration
@ComponentScan("com.example.mysample")
public class AppConfig {
    // ... @Bean 注释的方法
    @Bean
    public MyBean myBean(){
        return new MyBean();
    }
    ...
}
~~~

以上配置代码在 XML 中完全等同于如下：

~~~xml
<beans>
    <bean name="myBean" class="com.example.mysample.MyBeanImpl"></bean>
</beans>
~~~

> 被 Spring 应用上下文控制的 Spring bean 是单例类。



#### 2. @Autowired 和 @Bean 在自动配置中的区别

ref： [配置 @Configuration 并且在其中配置 @bean 的逻辑](https://stackoverflow.com/questions/32078600/why-do-i-not-need-autowired-on-bean-methods-in-a-spring-configuration-class)

@Configuration 注解放在一个配置类上，使得该配置类**处于应用上下文的“内部世界”**中，在其中找 bean 会自动在Spring IOC容器中找；而 @Aurowired 则声明将**初始化好的** bean 注入到**“外部世界”（应用程序）**中，因为是初始化好的，所以在外部程序中（在 @Configuration 注释之外）使用自动配置时，就不需要初始化 bean：

~~~java
// 使用了@Configuration 注释，下方的自动装配会自动在 Spring 应用上下文中查找 bean，不需要使用 @Autowired
@Configuration
public class jdbcConfiguration{
    ...
 	//  NamedParameterJdbcTemplate bean      
    @Bean
    public NamedParameterJdbcTemplate namedParameterJdbcTemplate(){
        NamedParameterJdbcTemplate retBean = new NamedParameterJdbcTemplate(dataSource());
        return retBean;
    }
}
~~~

~~~java
// 在外部世界（除了 @Configuration 注释外的应用程序中）使用自动装配注入，要使用 @Autowired
@Repository
public class StuRepository{
    // 在外部为 JdbcTemplate bean 进行自动装配注入
    @Autowired
    private NamedParameterJdbcTemplate sqlDao;
    
    ...
    
    public void addStu(StuModel stuToAdd){
        // 无需初始化 sqlDao
        sqlDao.update...
    }
}
~~~





基于 XML 的配置：

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

<bean class="com.example.bean.Student">
    <property name="field" value="sample-value"></property>
</bean>
</beans>
~~~

以上是包含一个 Student bean 的定义的 xml 文件。

要在 Spring Boot 应用中使用它，我们需要使用 @ImportResource 注释，@ImportResource 用于将 bean 从 XML 文件中加载到 Spring IOC 容器中：

~~~java
@Configuration
@ImportResource("Mybean.xml")
public class SpringBootXmlApplication implements CommandLineRunner{
    @Autowired
    private Student student;
    
    public staitc void main(String[] args){
        SpringApplication.run(SpringBootXmlApplication.class,arg;
    }
}
~~~

> 在 Spring 3.0 之前，XML 是定义和配置 bean 的唯一方法，Spring 3.0 引入了 JavaConfig，允许我们在 Java 类中配置 bean。



#### 资料来源

[1] [基于注解的配置](https://docs.spring.io/spring-framework/docs/3.0.0.M3/reference/html/ch04s11.html)

[2] [JavaConfig 组合式配置风格](https://docs.spring.io/spring-javaconfig/docs/1.0.0.M4/reference/html/ch06.html)

[3] [@Autowired 指南](https://www.baeldung.com/spring-autowire)

[4] [使用 @Configuration 的 Spring Java 配置示例](https://www.netjstech.com/2015/10/spring-example-program-using-javaconfig-annotations.html)

