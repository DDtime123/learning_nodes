# Java 函数式编程

Java 8 对函数式编程的支持主要来自于三个功能：

* 支持 [工作管道] 流。Streams 允许我们以功能方式通过管道中的多个阶段处理数据。我们可以从容器实例（例如 List）创建此类流，或是使用特定的 Stream 类（如 IntStream）来创建它们。
* 支持 lambda 函数。简单地说，这是一个匿名函数（无需给它命名），他接受许多参数并返回一个值。返回值将通过管道传递到下一个操作。
* 支持将函数传递给其他函数。

函数式编程能够利用多核处理器，轻松完成并行处理工作。

HelloWorld：

~~~java
public static void main(String[] args){
    
    List<String> countries = new Arrays.asList("France","India","China","USA","Germany");
    
    for(String country : countries){
        System.out.println("Hello "+country);
    }
}
~~~

此为使用增强型 for 循环打印语法。

如果我想要检查代码是否有效，是不是要将循环体代码复制一遍，在任何要用到的地方重复一遍？

如果想并行处理项目，将一个 country 交给一个线程，一个线程应该处理一个国家，还是一批？如何分批传递工作，并检查何时完成？

List 只是一个容器，不能理解要他们做的事情。

Java 8 提供流，流可以处理指令，听懂我们要他们做的事情，容器可以将它们的内容传递给流：

~~~java
public static void main(String[] args){
    List<String> countries = new Arrays.asList("France","India","China","USA","Germany");
    
    countries.stream().forEach(
    	(String country) -> System.out
        .println("Hello "+country+"!"));
}
~~~

.stream 将内容传递给 Stream ，.forEach 将内容拆分。forEach 这个迭代器，能够将内容拆分成批的工作。这种批处理，可以实现并行化。

Stream 是一个接口，由抽象类 ReferencePipeline 实现，然后将作业链接到返回的 Stream。每个作业都完成一些工作，完成后可能会调用下游作业。在示例中，列表创建了一个 Stream（**流**）并且一个 forEach （**作业**）被添加到它的管道中。-> 表示 lambda 表达式（匿名函数）。

因为只有一个参数所以可以省略类型：

~~~java
countries.stream().forEach(
    	(country) -> System.out
        .println("Hello "+country+"!"));
~~~

