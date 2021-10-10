# TypeScript 创建类

在TypeScript中使用 OOP 概念来定义一个类的方法：

~~~typescript
class Person{
    // 属性
    firstName: string;
    lastName: string;
    
    // 默认构造器
    constructor(){
    }
    
    // 带参数的构造方法
    constructor(fName: string,lName: string){
        this.firstName = fName;
        this.lastName = lName;
    }
    
    // getter方法
    getFullName(){
        return `${firstName} ${lastName}`;
    }
}
~~~

要访问类的属性，要使用 this 运算符。

~~~typescript
this.firstName;
this.lastName;
~~~

默认情况下，typeScript 类中的所以成员的访问修饰符都是 public 。

~~~typescript
class Person{
    // 属性
    public firstName: string;
    public lastName: string;
    
    // 默认构造器
    constructor(){
    }
    
    // 带参数的构造方法
    public constructor(fName: string,lName: string){
        this.firstName = fName;
        this.lastName = lName;
    }
    
    // getter方法
    public getFullName(){
        return `${firstName} ${lastName}`;
    }
}
~~~



#### TypeScript 中的构造函数有两种：

默认构造函数，不带参构造函数



#### 实例化 TypeScript 的类

~~~typescript
let person: Person = new Person("Kunal","Chowdury");
console.log(person.getFullName());
// 另一种实例化方法
let person = new Person("Kunal","Chowdury")
console.log(person.getFullName())
~~~



