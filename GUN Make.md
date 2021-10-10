# GUN Make

### 1. GUN Make概述

一般的大型项目可能包含数千行代码，分布在多个源文件中，由许多开发人员编写并排列在多个子目录中。一个项目可能包含多个组件，这些组件之间可能有复杂的依赖关系——例如，为了编译组件 X ；必须先编译 Y ；为了编译 Y ，必须先编译 Z ；等。对于大型项目，对少量源代码进行更改时，重新编译整个项目所耗费的时间和消耗是不可容忍的。

通过 Make 可以解决这个问题。它可以指定组件之间的依赖关系，记录源代码的修改时间，以满足依赖关系的顺序编译组件，以此达成仅仅编译需要重新编译的文件和依赖它的组件，从而节省大量的时间。Make 是大型软件项目不可缺少的工具。

### 2. 安装 GUN Make

大多数发行版不讲 make 作为默认安装的一部分，如果是在 Ubuntu 系统上，运行以下命令可安装 make 以及从源代码构建 make 所需的其他一些常用软件包：

~~~xml
sudo apt-get install build-essential
~~~

### 3. 一个示例项目

首先创建三个文件： module.h 和对应的源文件 module.c ，以及主文件 main.c 。

~~~c
// module.h
#include <stdio.h>
void sample_func();
~~~

~~~c
// module.c
#include "module.h"
void sample_func(){
    printf("Hello World!");
}
~~~

~~~c
// main.c
#include "module.h"
void sample_func();

int main(){
    sample_func();
    return 0;
}
~~~



然后编译项目，以下是使用 gcc 编译器编译项目并生成二进制可执行程序的完整过程，每个步骤都生成了各个源文件的目标文件：

![1632993073160](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632993073160.png)

（ **-I**  用于包含当前目录 ( **.** ) 作为头文件位置）



### 4. 使用 Make 命令和 Makefile

根据惯例，Makefile 中的变量应该是全大写字母，比如 CC = gcc。然后可以在 Makefile 里使用 ${CC} 的语法使用 gcc 命令。Makefile 的注释格式是 # 后加注释。

Makefile 的一般规则语法：

~~~makefile
target: dependency1 dependency2 ...

[TAB] action1

[TAB] action2

	...
~~~

我们为这个简单的示例项目构建一个 Makefile：

~~~makefile
all: main.o module.o
	gcc main.o module.o -o target_bin
	
main.o: main.c
	gcc -I . -c main.c
	
module.o: module.c module.h
	gcc -I . -c module.c
	
clean:
	rm -rf *.o
	rm target_bin
~~~

这个 Makefile 中一共有4个目标：

* all 是一个依赖于 main.o 和 module.o 的特殊目标，它的命令是让 GCC 将两个目标文件链接在一起形成最终的可执行二进制文件；
* main.o 是一个依赖于 main.c 的目标，具有编译生成目标文件 main.o 的命令；
* module.o 是一个依赖于 module.c 和 module.h 的目标，具有编的译生成目标文件 module.o 的命令；
* clean 是一个没有依赖关系的目标，它指定了从项目目录中清除编译输出的命令。

make 命令接收一个目标参数，参数必须是 Makefile 中定义的参数之一。语法是：

~~~makefile
make <target>
~~~

但是，make 命令即使没有被指定参数，make 命令也会执行，这时 make 会执行 Makefile 中定义的第一个目标。

### 5. Make 命令的执行过程

当执行 make 命令时，它会在当前目录中查找名为 Makefile 的文件。它会解析 Makefile ，并构建依赖树。根据在命令行上指定的 make 目标，make 检查该目标的依赖文件是否存在；如果它们存在，就比较文件时间戳，判断它们的更新时间是否在目标之后。

在执行所需目标对应的命令之前，必须满足其依赖关系（即所依赖的文件必须已存在并且已更新完毕），当它们不满足时，则必须提供它所需得依赖项（创建依赖项或者更新依赖项）。

当目标是文件名时，make 会比较目标文件和其依赖文件的时间戳，如果依赖文件名是 Makefile 中的另一个目标，则 make 会检查该目标依赖的时间戳。因此，它会一直递归地检查依赖树，直到每一个依赖文件都在其目标文件之前更新，最后再更新最终的目标文件。

当目标不是文件名时，make 无法通过比较时间戳来检查目标的依赖文件是否需要更新，但是如果依赖项有 Makefile 里的目标文件，则会对依赖项目标文件检查它的依赖项是否比它更新。

### 6. Makefile 的语法

#### 6.1 Makefile 变量

Makefile 中有多种分配变量的方法：

* 简单赋值 （:=），我们可以用这个操作符赋值，例如 CC := gcc ，当找到它的第一个定义时，该值被扩展并存储到 Makefile 中的所有匹配项。

* 递归复制 （=），递归赋值涉及变量和值，这些变量和值在遇到它们的定义时不会立即求值，而是在运行过程中遇到它们时会求值，例如：

~~~makefile
GCC = gcc
FLAGS = -W
~~~

以上这两行赋值，仅当在 Makefile 中的某处执行类似 CC = ${GCC} ${FLAGS} 时才会现场即时转换。而如果 GCC 变量在该条语句后边有 **另一条定义** GCC = c++ ，那么当它遇到下一条使用 ${GCC} 语句时，${GCC}就会替换为 c++。

* 条件赋值 （?=），只有当变量还没有值时，条件赋值语句才给变量赋值。

* 附加 （+=），附加操作将文本附加到现有变量，比如：

~~~makefile
CC = gcc
CC += -W
~~~

CC 现在的值为 gcc -W。

Makefile 中变量赋值可以出现在 Makefile 的任何地方，而变量的声明一般都要位于 Makefile 的开头。

 #### 6.2 Makefile 的模式和特殊变量

% 百分号可用于通配符进行模式匹配，以提供通用目标，例如：

~~~makefile
%.o %.c
[TAB] actions
~~~

当 % 出现在依赖项列表中时，它被替换为用于在目标中进行替换的相同字符串。

在 actions（命令） 内部，我们可以使用特殊变量来匹配文件名，例如：

* $@	（当前目标的完整目标名称）
* $?      （返回比当前目标更新的依赖项）
* $*      （返回 % 目标中对应的文本）
* $<      （第一个依赖项的名称）
* $^      （与空格为分隔符的所有依赖项的名称）

以上通配符可以用来编写更通用的 Makefile。

#### 6.3 动作修饰符

我们可以通过在行动前添加某些动作修饰符来改变我们使用的动作的行为，其中有两个重要的动作修饰符：

（-）减号，减号前缀添加到任何操作将会导致操作时发生的任何错误被忽略。默认情况下，当任何一个命令返回错误值时，Makefile 的执行将会停止，并且打印一条信息，而其中包含减号前缀的命令的错误将不会被打印。比如，在 clean 目标中，rm target_bin 命令当该文件不存在时，该命令将产生错误，为了解决这个问题，我们可以在 rm 命令前加上减号：

~~~makefile
-rm target_bin # 修改 rm target_bin
~~~

（@）艾特号，艾特号抑制 make 的标准打印操作中的一些打印操作。比如要将自定义消息回显到标准输出，我们有 echo Message 命令，但是我们只需要 Message 信息，而不需要打印 echo 这句命令，@echo Message 将打印出 Message 而不会将命令 echo 也一起打印出来。

#### 6.4 使用 PHONY 避免文件目标名称冲突

我们的 Makefile 中的目标可以是一些非文件名的目标，当这种非文件名的目标和某个文件名冲突了怎么办？比如在项目中有一个名为 all 或者 clean 的名称，这时就要用到 .PHONY 指令指定这些目标不被视为文件，比如：

~~~makefile
.PHONY: all clean
~~~

#### 6.5 Makefile 试运行

有时候，我们在开发 Makefile 时，希望并不真正运行命令，但是又想跟踪 make 命令执行，这时候就要使用 make -n 命令进行试运行：

~~~makefile
make -n
~~~

#### 6.6 在 Makefile 中使用 shell 命令输出

有时候我们需要在 Makefile 命令中使用一个 Linux 命令的输出，比如获取当前目录下文件 ls 命令，这时我们可以通过使用 $(shell ls) 在 Makefile 中获取 shell 的 ls 命令的输出。

#### 6.7 Makefile 嵌套

有的时候我们的项目包含一个或多个子目录，我们在这些目录里也建立了 Makefile ，我们并不想手动移动到这些目录并且一个个编译它们，为此，我们要通过在主目录中的 Makefile 里移动到这些子目录里并且编译它们：

~~~makefile
subtargets:
	cd subdirectory && $(MAKE)
~~~

我们没有运行 make 命令，而是使用了 $(MAKE) 这样一个环境变量，这是为了保证参数的灵活性（比如实际上我们想要进行试运行，就可以将 MAKE 设置为 make -n）。

现在我们使用以上高级功能改进我们的原始 Makefile ，使其具有通用性：

~~~makefile
CC = gcc # Compiler to use
OPTIONS = -02 -g -Wall # -g 开启Debug模式，-O2 设置编译器优化等级为2级，-Wall 禁止所有警告信息
INCLUDES = -I . # -I 用于包含某个目录作为头文件目录，这里为 (.) 当前目录
OBJS = main.o module.o # 将要构建的目标文件
.PHONY: all clean # 设置 all 和 clean 两个目标不被视为文件

all: $(OBJS)
	@echo "Building.." # 打印 Building.. 信息
	${CC} ${OPTIONS} ${INCLUDES} ${OBJS} -o target_bin
	
%.o: %.c # 使用通配符进行模式匹配
	${CC} ${OPTIONS} -c $*.c ${INCLUDES}
	
list:
	@echo ${shell ls} # 打印 ls 命令的输出
	
clean:
	@echo "Cleaning up..."
	-rm -rf *.o # 清除所有以 .o 结尾的文件，并且忽略掉其中可能产生的错误
	-rm target_bin # 清除目标文件，并且忽略可能产生的错误
~~~