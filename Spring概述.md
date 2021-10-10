#### Spring概述

##### 前言

>  任何应用程序都是由许多组件组成的，每一个组件负责整个应用功能的一部分，这些组件需要与其他元素协调以完成自己的任务，在应用程序运行时，需要以某种方式创建并引入这些组件。

Spring的核心提供了一个容器（container），通常称为Spring应用上下文（Spring application content），Spring应用上下文负责创建和管理应用程序的组件。通常这些组件也可称为bean。Spring应用上下文通过基于依赖注入（dependency injection，DL）的方式将bean装配在一起。这时，组件不再创建和管理组件自身的生命周期，而完全交由容器创建和管理，并将其注入到需要它们的bean中。通常这是通过**构造器参数**和**属性访问方法**实现的。



###### 假设有两个组件需要处理，商品服务（用来提供基本商品信息）和库存服务（用来获取商品水平），而商品服务依赖于库存服务才能提供完整的商品信息

![应用组件通过Spring应用上下文进行管理并实现相互注入](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631146864954.png)

​									1.1 应用组件通过Spring应用上下文进行管理并实现相互注入



Web框架，多种持久化方案支持，网络安全框架，与其他系统集成，运行时监控，微服务支持，反应式编程



历史上知道Spring应用上下文将bean装配在一起，是通过一个或多个xml文件描述各个组件以及组件之间的关联关系。如下的xml描述了两个bean，InventoryService bean和ProductService bean，通过**构造器参数**将InventorySerive装配到ProductService中：

![1631150240825](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631150240825.png)

但是，在最近的Spring版本中，**基于java的配置**更为常见，以下java代码与xml配置方式等价：

![1631150549232](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631150549232.png)



##### 基于java的配置代码解析

* @Configuration注解会告知这是一个配置类，会为Spring应用上下文提供bean。

* @Bean注解添加到配置类的方法中，表明这些方法返回的对象将以bean的形式添加到Spring应用上下文中，默认情况下这些bean的bean ID与定义它们的方法名称相同。

* 基于java的配置方式比起基于xml的配置方式增加了类型安全性和重构能力



##### 无论是xml还是java这两种显示配置方式，仅当Spring不能自动配置是必要的。

Spring借助组件扫描和自动装配技术完成自动配置。Spring能够自动发现应用类路径下的组件，将它们创建成bean，Spring能够为组件自动注入他们所依赖的其他bean。

Springboot能够基于类路径中的条目，环境变量和其他因素合理猜测需要装配在一起的组件并将它们装配到一起。











