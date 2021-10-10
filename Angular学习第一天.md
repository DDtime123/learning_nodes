### Angular学习第一天

#### 1. 仿制 Reddit 网站

本篇中，我们要构建一个应用，它拥有让用户 **发表推荐文章**（包括标题和URL）并 **对每篇文章投票**。

这个简单的应用将涵盖 Angular 中的大部分要素，包括：

* 构建自定义组件
* 从表单中接收输入
* 把对象列表渲染到视图中
* 拦截用户的点击操作，并据此做出反应

该项目将使用 TypeScript 语言。TypeScript 是 JavaScript ES 6 版的一个超集，增加了类型支持。

在使用 TypeScript 之前必须先要安装 Node.js ，在安装好 Node.js 之后，运行以下 npm 命令，即可安装 TypeScript ：

~~~xml
npm install -g typescript
~~~

接下来安装 angular-cli，angular-cli 是 Angular 提供的一个命令行工具，它能够让用户通过命令行创建和管理项目。它提供了一系列自动化任务，比如创建任务，添加新的控制器等。

要安装 angular-cli，请运行下列命令：

~~~xml
npm install -g angular-cli
~~~

安装完成后，运行 ng -v 命令，就可以查看当前 angular-cli 的版本号：

![1632734150790](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632734150790.png)

我们使用 Visual Studio Code（简称 VS Code） 作为开发工具，该软件是一款免费的跨平台开发工具，可以在官网下载[Visual Studio Code 官网](https://code.visualstudio.com/)。

现在环境我们已经搭建好了，接下来开始编写第一个 Angular 应用。

#### 2. 新建示例项目

在 VS Code 中打开一个文件夹，打开终端，终端所在目录会自动跳转到文件夹内：

![1632736509330](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632736509330.png)

然后使用 ng 命令新建一个 Angular 项目，注意项目名称中不能带有下划线：

~~~xml
ng new angular2HelloWorld
~~~

等待 Angular 将项目所需依赖项下载下来，项目初始化就成功了， Angular 还会将项目初始化为一个 git 项目：

![1632736710186](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632736710186.png)

接下来查看项目的目录结构：

![1632743925997](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632743925997.png)

我们来关注 src 目录，应用的代码就在里面

![1632745079113](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632745079113.png)

打开 index.html ，这就是我们看到的代码：

~~~html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Angular2HelloWorld</title>
  <base href="/">

  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
</body>
</html>
~~~

现在来分析它：

~~~html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Angular2HelloWorld</title>
  <base href="/">
~~~

上面是一段在 HTML 中很常见的声明代码，其中声明了页面的字符集（charset），标题（title）和基础URL（base href）。

页面其中值得注意的是页面内容部分（body）：

~~~html
<body>
  <app-root></app-root>
</body>
~~~

页面的内容部分使用了自定义标签 app-root ，这是我们的应用进行渲染的地方。

接下来运行应用，angular-cli 为我们提供了一个内置 HTTP 服务器，我们可以通过在项目的根目录处（./angular2HelloWorld）运行 ng serve 命令开启服务器：

~~~xml
ng serve
~~~

等待程序编译完成后：

![1632746790022](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632746790022.png)

编译完成后，打开网址 localhost:4200，Angular 提供了一个初始应用：

![1632746884286](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632746884286.png)



#### 3. 制作Angular 组件

Angular背后的指导思想之一就是 **组件化**。

在 HTML 页面中，我们通过 HTML 标签给网页添加交互功能，但是 HTML 原生的标签是有限的，浏览器只认识那些由浏览器的开发者预先定义好的标签，比如 select，form，video 等。

如果我们想要在 HTML 页面里用上新的标签，在标签里定义新的功能，比如有一个 weather 标签能够显示天气，还要让浏览器正确地认识它们，那该怎么办呢？

Angular 的组件化的基本思想，就是教浏览器认识一些有新功能的标签。



##### 3.1 创建第一个 Angular 组件

首先，我们来创建一个 Angular 组件，使用 angular-cli 创建 Angular组件，要用到 generate 命令，在项目根目录运行下列命令：

~~~xml
ng generate component hello-world
~~~

![1632748086619](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632748086619.png)

angular-cli 创建 Angular 组件，首先在 src/app/ 目录下创建了一个与组件名称相同的文件夹 hello-world，然后在里面创建了4个文件，并且在 src/app/ 目录下的 app.module.ts 文件中加入了新添加的组件的信息。



##### 3.2 分析组件的代码

首先，我们查看新创建的 src/app/hello-world/ 目录中的 hello-world.component.ts 文件：

~~~typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-hello-world',
  templateUrl: './hello-world.component.html',
  styleUrls: ['./hello-world.component.css']
})
export class HelloWorldComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
~~~

这里定义了一个组件 HelloWorldComponent ，我们来一句句分析一下这份代码。



(1) 导入语句

~~~typescript
import { Component, OnInit } from '@angular/core';
~~~

首先是导入依赖的语句，import 语句定义了我们写代码时要用到的模块，这里我们从 @angular/core 模块导入了 Component 以及 OnInit 两个组件，from @angular/core 语句告诉编译器到哪里寻找程序所需的这些依赖。

Component 和 OnInit 是两个 TypeScript 对象，其中 OnInit 对象能够帮助我们在组件的初始化阶段运行某些代码。

import 语句的语法是 import { things } from wherever，其中 { things } 这部分写法叫做解构，是由 ES6 和 TypeScript 提供的一项新特性。



(2) 注解

接下来看第二段，这是组件的声明语句：

~~~typescript
@Component({
  selector: 'app-hello-world',
  templateUrl: './hello-world.component.html',
  styleUrls: ['./hello-world.component.css']
})
~~~

如果有过 Java 开发经验的人会感到很熟悉，因为这就是注解。

注解可以被理解为 **添加到代码之上的元数据**。当在 Hello-world 类上添加 @Component 注解时，它就被装饰成了一个组件（Component）。

看看第一句：

selector 属性表示我们想要在 HTML 中通过什么名称的标签使用这个组件，在这里是 app-hello-world ，所以可以通过在 HTML 中添加下列代码来使用组件：

~~~html
<app-hello-world></app-hello-world>
~~~

总之 selector: "app-hello-world" 定义了一个名为 app-hello-world 的新标签。

再看第二句：

~~~typescript
templateUrl: './hello-world.component.html',
~~~

templateUrl 指定我们将从哪个文件中加载模板，这个模板将会被加载到任意引用 app-hello-world 标签的地方。

接下来查看模板文件 hello-world.component.html ：

~~~html
<p>
  hello-world works!
</p>
~~~

当 Angular 加载组件时，这段 HTML 文本将被读取并作为模板的内容。

其实还有一种加载模板的方式，那就是内嵌模板。并非使用 templateURl 引用外部模板文件，而是使用 template 属性，直接将模板嵌入代码中，具体方法如下：

~~~typescript
@Component({
  template:`
    <p>
      hello-world works inline!
    </p>
  `,
    ...
})
~~~

其中包围着模板代码的是反引号 `` ，在里面可以使用多行字符串，这是 ES6 的一个新特性。

看看第三句：

~~~typescript
styleUrls: ['./hello-world.component.css']
~~~

这段代码声明了我们使用什么 CSS 样式表作为该组件的样式。Angular 使用一种叫做样式封装的技术让每个组件中指定的样式只会作用于该组件本身。当然，使用 [] 是指定数组作为数值的表现，我们可以在 [] 中为组件指定多个样式表。



(3) 组件类

再看第三段：

~~~typescript
export class HelloWorldComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
~~~

这就是一段稀松平常的 TypeScript 中定义类的代码，实现了一个 OnInit 接口，并通过 export 声明让类在外部可用。



#### 4. 加载组件

现在，第一个组件的代码已经完成了，接下来该如何使用它呢？

在 Angular 应用启动时，Angular 将 src/app/app.component.html 作为模板渲染以便，连带着渲染在 src/app/app.component.html 中引用的组件模板。所以要想使用自定义的组件，只需要在 src/app/app.component.html 中通过 HTML 标签引入该组件就可以了，刚刚说过，在组件的注解 @Component 中 selector 属性的值，就是组件对应的标签名称。

修改 src/app/app.component.html 代码如下：

~~~html
<div style="text-align:center">
  <h1>
    Welcome to {{ title }}!
  </h1>
</div>
<!-- selector: 'app-hello-world' -->
<app-hello-world></app-hello-world>
~~~

编译并运行：

~~~xml
ng serve
~~~

浏览器效果如下：

![1632791399450](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632791399450.png)



#### 5. 在组件中添加数据

新添加一个组件 user-item，我们设想这个组件将加载一个用户列表，用户的数据由服务端提供。

~~~xml
ng generate component user-item
~~~

首先我们要先渲染单独一个用户，打开新组件的源文件 user-item.component.ts，我们希望 UserItemComponent 是用户相关的类。

UserItemComponent 应该指定用户的姓名，在 UserItemComponent 类中添加一个 name 属性：

~~~typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-user-item',
  templateUrl: './user-item.component.html',
  styleUrls: ['./user-item.component.css']
})
export class UserItemComponent implements OnInit {

  name: string; // <-- added name property

  constructor() {
      this.name="Felipe";
  }

  ngOnInit() {
  }

}
~~~

此处，我们往 UserItemComponent 中添加了一个属性，name : string 是 TypeScript 的语法，它要求 name 的类型为 string 类型，这种强制类型要求在 ES5 JavaScript 中是没有的。然后在构造器 constructor 中将 “Felipe” 赋值给了局部变量 name 。

在 UserItemComponent 类中定义了 name 属性后，要怎么在模板中使用这个类的属性呢？

首先，可以通过在模板中直接使用 {{  }} 双花括号运算符，这个运算符中的代码会以 TypeScript 表达式的形式被运行。

而且由于在组件类 UserItemComponent 的注解 @Component 中声明了这个类的模板文件 templateUrl: './user-item.component.html'，所以在这个模板文件中，{{  }} 双花括号运算符中的表达式将被视为在 UserItemComponent 内部的表达式，比如：

~~~html
<!-- user-item.component.html -->
<p>
    Hello {{ name }} !
</p>
~~~

将被视为在 UserItemComponent 内部使用 this.name 表达式，而表达式的输出将替换 {{ name }} 处的内容。

接下来，在 app.component.html 中插入该组件：

~~~html
<div>
  <h1>
    Welcome to {{ title }}!
    <!-- selector: 'app-hello-world' -->
    <app-hello-world></app-hello-world>
    <app-user-item></app-user-item>
  </h1>
</div>
~~~

编译运行程序：

![1632808763331](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632808763331.png)



#### 6. 使用数组

上面往 Angular 应用中添加了一个单独的用户数据，但是如果想要添加一组用户数据，那该用什么办法呢？Angular 提供了 NgFor 指令，它允许我们使用同一对象反复渲染同样的页面脚本。

下面创建一个用来渲染一组用户的组件——一个列表：

~~~xml
ng generate component user-list
~~~

接下来修改组件的代码 user-list.component.ts :

~~~typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-user-list',
  templateUrl: './user-list.component.html',
  styleUrls: ['./user-list.component.css']
})
export class UserListComponent implements OnInit {

  names: string[]; // list of names of users

  constructor() { 
    this.names=["Ari","Carlos","Felipe","Nate"];
  }

  ngOnInit() {
  }

}
~~~

我们往新建的 UserListComponent 组件类中添加了一个 names 属性，这个 names 是一个 string 类型的数组，除了用 string[] 的语法声明数组外，还可以用 Array< string>  的语法声明数组，其效果相同。然后在构造器中将4个字符串赋值给了 names。

接下来要使用这些 names 里的 name，往模板中加入双花括号 {{  }} ，使用 ngFor ，它会在一个列表上迭代，为这个列表中的每个条目创建一个新标签，并声明一个本地变量 name 来持有当前迭代的条目，然后这个新变量被插入 {{ name }} 里。

修改 user-list 组件的模板文件：

~~~html
<!-- user-list.component.html -->
<ul>
    <li *ngFor="let name of names">Hello {{ name }}</li>
</ul>
~~~

这里我们使用了一个 ul 和一个添加了 *ngFor="let name of names" 属性的 li 元素来更新模板。

*ngFor 语法是说我们想要在这个属性上使用 NgFor 指令，其目的是为集合中的每一个条目都新建一个 DOM 元素。

它的值是 “let name of names” 。names 是在 UserListComponent 对象中创建的数组，let name 叫做引用，“let name of names” 会循环遍历 names 数组中的每一个元素，然后将其赋值给 name 这个局部变量。

NgFor 指令将为数组 names 每一个条目渲染出一个 li 标签。

在 src/app/app.component.html 中调用新组件：

~~~html
<div>
  <h1>
    Welcome to {{ title }}!
    <!-- selector: 'app-hello-world' -->
    <app-user-list></app-user-list>
  </h1>
</div>
~~~

编译并运行程序：

![1632810447230](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632810447230.png)

#### 7. 使用 UserItemComponent 对象数组

仅仅在 UserListComponent  里使用 string 数组 names 只能够满足渲染每个用户的名字的功能，而如果要在模板中渲染每个用户的完整信息，最好的办法是在 UserListComponent 里引入用户组件 UserItemComponent ，然后创建 UserItemComponent 类型的数组用以替代 names 数组。在这里 UsetItemComponent 被称为 UserListComponent 的子组件。

在 UserListComponent 中使用 UserItemComponent，有三个步骤：

(1) 在 UserListComponent 的模板文件中渲染 UserItemComponent

(2) 配置 UserItemComponent 接收 name 变量作为输入

(3) 配置 配置 UserListComponent 的模板将 name 参数的值传递给 UserItemComponent



第一步，在 UserListComponent 的模板文件中渲染 UserItemComponent：

~~~html
<!-- user-list.component.html -->
<ul>
  <app-user-item *ngFor="let name of names"></app-user-item>
</ul>
~~~

*ngFor="let name of names" 会根据 UserListComponent 中 names 数组的长度重复标签 app-user-item ：

![1632876030019](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632876030019.png)





第二步，接下来我们要将数据传递给子组件 UserItemComponent。

将 UserItemComponent 里的 name 的赋值方式从在构造函数中赋值改为接收外界参数赋值。

Angular 为接收外界参数提供了 @Input 注解：

~~~typescript
// user-item.component.ts
import { Component, OnInit,Input } from '@angular/core';  // <- 新引入 Input 组件

@Component({
  selector: 'app-user-item',
  templateUrl: './user-item.component.html',
  styleUrls: ['./user-item.component.css']
})
export class UserItemComponent implements OnInit {

  @Input() name: string; // <-- added name property，添加 @Input 注解，让 name 属性能够接收外界参数

  constructor() {
	// <- 移除了对 this.name 的赋值语句
  }

  ngOnInit() {
  }

}
~~~

我们在 UserItemComponent 的 name 属性上添加了 @Input 注解，这让它能够接收由父组件传递过来的值。



第三步，将一个值从模板传入组件的语法是方括号 [] ，而如果值是从 **父组件** 传入子组件，要在子组件的变量上添加 @Input 注解。

> Angular 中父组件和子组件的关系可以通过在父组件中使用子组件来确立。比如在这个例子中，我们在 UserListComponent 的模板文件中使用了 app-user-item 标签，所以 UserListComponent 是父组件，UserItemComponent 是子组件。

在父模板中传递值给子组件：

~~~html
<!-- user-list.component.html -->
<ul>
  <app-user-item *ngFor="let name of names" [name]="name"></app-user-item>
</ul>
~~~

在 app-user-item 标签中添加了新属性 [name]="name" ，其中 [name] 表示 app-user-item 组件上的添加了 @Input 注解后的名称为 name 的变量，而 “name” 则是 “let name of names” 中由父组件中的 names 迭代得来的的临时变量 name 。

现在我们已经完成了渲染名字列表的任务，主要的步骤：

(1) 在 names 中迭代；

(2) 为每一个组件创建一个 UserItemComponent ；

(3) 将每一个 UserItemComponent 单独渲染一个标签并显示在 UserListComponent 的模板上；

运行程序：

![1632879939255](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632879939255.png)



#### 8. Angular 启动



