### Java设计模式

#### 1. 单例模式

> 单例模式是设计模式中最常见，最简单的模式。如Windows任务管理器就属于单例模式，任何时刻都只能打开唯一的任务管理器对象。

如何实现一个类只能创建唯一的对象呢？首先，需要一个属性来保存唯一的对象；其次，需要将构造方法隐藏起来，不允许用户通过 new 调用构造器去创建对象；最后，设计一个获取对象的方法，每次调用该方法时，返回同一个对象。

单例模式主类如下：

~~~java
public class Singleton {

    // 私有的静态的属性
    private static Singleton instance;

    // 私有的构造器
    private Singleton(){}

    // 公开的静态的获取实例方法
    public static Singleton getInstance(){
        if(null == instance)
            instance=new Singleton();
        return instance;
    }
}
~~~

测试单例模式：

~~~java
package java;

public class SingletonTest {
    public static void main(String[] args) {

        // 获取Singleton的一个对象 s1
        Singleton s1=Singleton.getInstance();

        // 获取Singleton的另一个对象 s2
        Singleton s2=Singleton.getInstance();

        // 判定两个对象为同一对象
        System.out.println(s1);

        System.out.println(s2);

        System.out.println(s1==s2);
    }
}
~~~

程序的编译运行结果如图，Singleton的构造器是私有的，外界程序不能通过new来调用该类的构造器，只能通过Singleton类的静态方法getInstance，返回同一个对象。

![1632134621739](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632134621739.png)

#### 2. 工厂模式

> ​		在面向对象程序设计时产生一个对象实力，常用的方法时使用new操作符，但是在一些情况下直接使用new操作符生成对象，也会带来不便。比如，许多类型对象的创造不是一蹴而就的，它需要一系列的步骤：用户可能需要计算或者取得对象的初始设置，或在生成所需对象之前，先生成一些辅助功能的对象。

在这些情况下，新对象的建立已不仅仅是一个孤立的操作，而是一个过程，就像一个产品的出厂一样，牵动了一系列车间的配合和联动。

所面临的问题是：

如何能便捷地构造对象实例，而不必关心构造对象实例的细节和复杂过程呢？

解决方案：

建立一个工厂来创建对象，这就是工厂设计模式。

##### 2.1 简单工厂模式

俄罗斯方块例子：

~~~java
package com;

interface Block{
    public void print();
}

class IBlock implements Block{
    public void print(){
        System.out.println("我是一个 I 型的方块！");
    }
}

class LBlock implements Block{
    public void print(){
        System.out.println("我是一个 L 型的方块！");
    }
}

class Factory{
    public static Block getInstance(String classname){
        switch (classname){
            case "IBlock":return new IBlock();
            case "LBlock":return new LBlock();
            default:return null;
        }
    }
}

public class TestSimpleFactory {
    public static void main(String[] args) {

        // 用工厂模式生产一个I型方块
        Block iBlock = Factory.getInstance("IBlock");

        // 用工厂模式生产一个L型方块
        Block lBlock = Factory.getInstance("LBlock");

        iBlock.print();
        lBlock.print();
    }
}
~~~

通过定义一个工厂类Factory，在工厂里有两个模拟的“车间”，然后有Factory类生产出来方块。

范例分析：

现在来总结一下简单工厂模式中包含的角色以及其相应的职责：

工厂（Factory）角色：

这是简单工厂模式的核心所在，它负责创建所有具体产品类的内部生产逻辑，提供方法被外界调用，从而让外界定制所需产品。

抽象产品（Abstract Product）角色：

这个角色，其实就是简单工厂模式所创建的所有对象的父类（如本例中的接口Block），这个类既可以是接口，还可以是一个抽象类，他负责描述所有实例的公有接口。

具体产品（Concrete Product）角色：

简单工厂所创建的具体产品类（如本例中的IBlock和LBlock），这些具体的产品类，往往具有共同的父类。

优点：客户端（在本例中是主类TestSimpleFactory）使用简单，不需要修改代码。

缺点：当要增加新的运算类时，不仅要新加运算类，还要修改工厂类。

##### 2.2 高级工厂模式

随着技术的进步，简单工厂模式已经跟不上时代的进步，为了满足多元化的需求，产品的系列越来越多，单独一个工厂是无法创建所有系列的产品的。

于是，就会从一个总厂裂变，分出来多个单独的具体工厂，每个具体的工厂只创建一种类型的产品，这就是高级工厂模式，也是现在的标配工厂模式。

以下还是用俄罗斯方块说明：

~~~java
package com;

interface Block{
    public void print();
}

class IBlock implements Block{
    public void print(){
        System.out.println("我是一个 I 型的方块！");
    }
}

class LBlock implements Block{
    public void print(){
        System.out.println("我是一个 L 型的方块！");
    }
}

interface Factory{
    public Block getInstance();
}

class IBlockFactory implements Factory{
    @Override
    public Block getInstance(){
        return new IBlock();
    }
}

class LBlockFactory implements Factory{
    @Override
    public Block getInstance(){
        return new LBlock();
    }
}

public class TestAdvancedFactory {
    public static void main(String[] args) {

        // 创建一个生产I型方块的工厂
        Factory iBlockFactory = new IBlockFactory();

        // 用工厂生产一个I型方块
        Block iBlock = iBlockFactory.getInstance();

        // 创建一个生产L型方块的工厂
        Factory lBlockFactory = new LBlockFactory();

        // 用工厂生产一个L型方块
        Block lBlock = lBlockFactory.getInstance();

        iBlock.print();
        lBlock.print();
    }
}
~~~

![1632137900246](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632137900246.png)

范例分析：

若用户想要增加一个新的方块子类，这个子类只需要新实现一个Block接口，并创建一个新的相应工厂类来实现Factory接口即可。这非常符合软件工程里的“开闭原则”。

所谓开闭原则，就是对外扩展开放，对修改关闭。

针对本例来说，对已经开发好的IBlock和IBlockFactory，以及LBlock和LBlockFactory这些模块是封闭的，对这些已经行之有效的功能，我们不去招惹它们。但是新需求和新功能，我们能做到扩展。遵循开闭原则，能让我们的软件开发更好地做到代码的维护和修改。

#### 3. 总结

本篇章学习了Java的单例模式和工厂模式，学会运用设计模式是让代码真正工程化，实用化的前提。好的软件无一不用上好的设计模式，好的工程师也无一不会设计模式。