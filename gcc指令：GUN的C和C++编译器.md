### gcc指令：GNU的C和C++编译器

#### 1. GCC简介

> GNU CC（简称 gcc）是GNU项目中符合ANSI C标准的编译系统，能够编译用C，C++和Object C等语言编写的程序。

#### 2. gcc编译过程

gcc的编译分为如下4个步骤：

* 预处理：主要进行宏替换以及头文件的包含展开，不会检查错误.

gcc -E HelloWorld.c -o HelloWorld.i

* 编译：编译生成汇编文件，会检查语法是否有错误。

gcc -S HelloWorld.i -o HelloWorld.s

* 汇编：将汇编文件编译生成目标文件（二进制文件）

gcc -c HelloWorld.s -o HelloWorld.o

* 链接：链接库函数，生成可执行文件

gcc HelloWorld.o -o HelloWorld

编译器通过文件的扩展名决定对文件的处理方式

![1631779796805](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631779796805.png)

#### 3. gcc编译C代码示范

1. 首先创建 main.c 和 sub1.h文件，分别加入如下代吗

```c
/* main.c */
#include <stdio.h>
#include "sub1.h"

int main(){
	int i_x1,i_x2;
    scanf("%d %d",&i_x1,&i_x2);
	printf("%f\n",x2x(i_x1,i_x2));

	return 0;
}

```

~~~c
/* sub1.h */
float x2x(int a,int b){
	return (float)a-(float)b;
}
~~~

2. 进行编译

   * 预处理

     ~~~
     gcc -E main.c -o main.i
     ~~~

   * 编译

     ~~~
     gcc -S main.i -o main.s
     ~~~

   * 汇编

     ~~~
     gcc -c main.s -o main.o
     ~~~

   * 链接

     ~~~c
     gcc main.o -o main
     ~~~

3. Ubuntu环境下编译运行

![Linux下gcc编译命令](C:\Users\Administrator\Desktop\新建文件夹\Linux下gcc编译命令.png)

4. Windows下使用MinGW gcc编译器编译运行

![1631792514863](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631792514863.png)



### 使用make 和 Makefile进行C程序编译

#### 1. make简介

> make是一个自动编译管理器，能够根据文件时间戳自动发现更新过的文件而减少编译的工作量。它通过读入Makefile文件的内容来执行大量的编译工作，用户仅需一次编写简单的编译语句即可。

#### 2. Makefile

Makefile时make读入的唯一配置文件，一个Makefile通常包含以下内容：

* 需要由make工具创建的目标体，通常是目标文件或可执行文件。
* 需要创建的目标文件的依赖文件。
* 创建每个目标文件时要运行的命令，这一行必须以制表符（TAB）开头。

具体格式为：

~~~makefile
target : dependecy_files
	command /* 该行必须以制表符开头 */
~~~

例如，有两个文件 hello.c 和 hello.h ，创建的目标体为hello.o，执行命令为 gcc -c hello.c，对应的Makefile就应该写成：

~~~makefile
hello.o: hello.c hello.h
	gcc -c hello.c -o hello.o
~~~

接着就可以使用make命令 make hello.o

make将执行hello.o 目标体的命令语句，并生成hello.o 目标体。

##### 1. Makefile的变量

make允许在Makefile中创建和使用变量，变量在Makefile中可代替一串文本字符串，字符串为该变量的值。

变量定义有两种方式：递归展开方式和简单方式

递归展开方式：如果变量包含了对其他变量的引用，那么在引用该变量时，一次性将内嵌的变量全部展开。但有可能会导致无限循环。定义方式为：VAR=var

简单方式：在定义处展开，且仅展开一次，消除变量的嵌套调用。定义方式为：VAR:=var

make中变量的使用方式为： $ (VAR)

变量名是不包含 ":" "#" "=" 以及结尾空格的任何字符串，但应尽量避免包含除字母，数字和下划线的变量名，以免将来它们被赋予特别的含义。

例如：

~~~makefile
OBJS = kang.o yul.o
CC = gcc
CFLAGS = -Wall -O -g
david: $(OBJS)
	$(CC) $(OBJS) -o david
kang.o: kang.c kang.h
	$(CC) $(CFLAGS) -c kang.c -o kang.o
yul.o: yul.c yul.h
	$(CC) $(CFLAGS) -c yul.c -o yul.o
~~~

该处变量以递归方式定义，其中OBJS为用户自定义变量

##### 2. 自动变量

常见的自动变量

| 自动变量 | 含义                                               |
| :------: | -------------------------------------------------- |
|    S*    | 不包含扩展名的目标文件名称                         |
|    S+    | 所有的依赖文件，以空格分开，以出现的先后为序       |
|    S<    | 第一个扩展文件的名称                               |
|    S?    | 所有时间戳比目标文件晚的依赖文件，以空格分开       |
|    S@    | 目标文件的完整名称                                 |
|    S^    | 所有不重复的依赖文件，以空格分开                   |
|    S%    | 如果目标是归档成员，则该变量表示目标的归档成员名称 |

#### 3. Ubuntu下使用Makefile编译程序

Makefile如下

~~~makefile
main: main.o
	gcc main.o -o main
	
main.o: main.s
	gcc -c main.s -o main.o
	
main.s: main.i
	gcc -S main.i -o main.s
	
main.i: main.c main.h
	gcc -E main.c -o main.i
	
clean:
	rm *.i *.s *.o
~~~

执行 make main 命令

![Ubuntu下使用Makefile编译程序](C:\Users\Administrator\Desktop\新建文件夹\Ubuntu下使用Makefile编译程序.png)

### 总结

本文介绍了Linux环境下gcc编译器对C语言程序的编译过程，以及介绍了自动编译工具make的使用。