# GNU GCC（二）

**大纲：**

* 使用 gcc 在 Linux 上创建静态库动态库编译
* 使用 MinGW 在 Windows 上创建动态库和静态库
* 问题
* 大纲



**gcc 标志：**

* -c：禁止链接器调用
* -o：自定义输出文件
* -L：表示要引用的库的位置（.表示当前目录）
* -l：指定要附加的特定库
* -shared：告诉编译器想编译一个共享库

**ar 标志：**

* -r：将文件成员插入存档（带替换）
* -c：表示正在创建存档文件
* -s：将目标文件索引写入存档，对存档进行更改

GCC 编译器要求使用关键字 lib 和后缀 .a 作为静态库的前缀，例如 lib add.a 。

### 1. Linux 静态库和动态库

静态库平时作为单独文件存在，编译时被加载入执行文件中。

动态库或共享库在可执行文件之外单独存在。

静态库如果修改，可执行文件也需要修改，而动态库修改则不一定需要修改可执行文件。

使用静态库是每个应用程序之中都有库的一个副本

使用动态库时所有正在运行的应用程序都使用同一个库，无需每个应用程序都有副本，可执行程序大小更小。

#### 1.1 Linux 中创建动态库

第一步，将 .c 源文件做处理。由于多个程序可以使用动态库的同一实例，因此该库无法在固定地址存储数据，这是因为库在内存中的位置因程序而异。这是通过使用编译器标志 **-fpic** 来完成的。由于要在编译过程生成目标代码后应用此步骤，因此必须告诉编译器停止并且为每一个源文件返回一个目标文件 .o ，因此通过 -c 标志完成：

~~~makefile
gcc *.c -c -fpic
~~~

第二步，通过 -shared 标志编译所有 .o 文件。然后在稍后编译程序文件时，编译器会通过查找以 “lib” 开头并以库扩展名结尾（.so 动态库或 .a 静态库）的文件来识别库：

~~~makefile
gcc *.o -shared -o liball.so
~~~

第三步，将位置添加到环境变量 LD_LIBRARY_PATH 里，这是最后执行使用共享库的程序的必要步骤，如果不加入而执行最后的可执行程序，会出现以下错误：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211017171432.png)

可以通过 ldd my_program 来打印共享库依赖项：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211017172318.png)

注意其中的 liball.so => not found ，我们的 liball.so 没能被找到，这是因为默认情况下，程序加载器仅搜索 /etc/ld.so.conf 中列出的目录，因此我们可以通过设置 $LD_LIBRARY_PATH 环境变量来解决这个问题：

~~~makefile
export LD_LIBRARY_PATH=$PWD:$LD_LIBRARY_PATH
~~~

这会让程序加载器优先搜索当前目录$PWD

#### 1.2 Linux 中创建静态库

首先使用相同的方式创建目标文件：

~~~makefile
gcc *.c -c -fpic
~~~

然后使用以下语句进行归档库：

~~~makefile
ar rcs liball.a *.o
~~~

#### 1.3 使用库进行程序编译：

与外部库进行链接：

静态库 .a 文件是使用单独的工具 GNU 归档器从目标文件创建的，并由链接器用于在解析时对函数的引用。

-l 告诉编译器我们向包含库文件，-L 标志以及后边加的目录（这里是 . ）告诉编译器库文件在哪里，all告诉他寻找库 liball.so ，将 “lib” 和 “.so” 排除在标志外很重要，因为编译器会通过这种方式识别库文件：

~~~make
gcc -L. -lall -o my_program main.c
~~~



### 2. Windows 使用 MinGW 静态库和动态库

#### 2.1 MinGW 创建动态库

这里使用 Windows 平台的 GUN 编译器 MinGW 创建 dll 动态库：

现在提供一个头文件和一个源文件：

~~~c
// mingw_dll.h
#ifndef MINGW_DLL_H__
#define MINGW_DLL_H__

struct STRUCT_DLL {
    int count_int;
    int *ints;
};

int func_dll(
    int an_int,
    char* string_fill_in_dll,
    struct STRUCT_DLL* struct_dll
);

#endif
~~~

~~~c
// mingw_dll.c
#include <stdio.h>
#include <string.h>

#include "mingw_dll.h"

int func_dll(
    int an_int,
    char* string_filled_in_dll,
    struct STRUCT_DLL* struct_dll
){
    int i;

    printf("\n");
    printf("func_dll 被调用\n");
    printf("---------------\n");
    printf("an_int=%d\n",an_int);

    strcpy(string_filled_in_dll, "DLL 中填充的字符串");

    printf(" count_int=%d: ",struct_dll->count_int);
    for(i = 0;i < struct_dll->count_int;i++){
        printf(" %d",struct_dll->ints[i]);
    }
    printf("\n");

    printf("------------------\n\n\n");

    return 2*an_int;
}
~~~

现在对它们进行编译，生成动态库文件：

~~~c
gcc -Wall -shared mingw_dll.c -o mingw_dll.dll
~~~

最后是使用动态库：

~~~c
// use_mingw_dll.c
#include <stdio.h>

#include "mingw_dll.h"

int main(){
    char str[21];
    struct STRUCT_DLL s;
    int i;

    s.count_int = 10;
    s.ints = malloc(sizeof(int) * 10) ;

    for(i = 0;i < 10;i++){
        s.ints[i] = i;
    }

    func_dll(42, str, &s);

    printf("str: %s\n", str);
}
~~~

接着进行执行程序的编译：

~~~c
gcc use_mingw_dll.c mingw_dll.dll -o use_mingw_dll
~~~

执行输出：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211017142409.png)

> 这里注意，Windows 控制台对 UTF-8 的支持有限，我们可以通过将代码页设置为 65001 并使用支持它的字体之一，在控制台输入以下命令： chcp 65001



> DLL （C++）甚至可以被 C# 代码调用

#### 2.2 MinGW 创建静态库

使用 Mingw 构建静态库：

~~~makefile
gcc -c mingw_dll.c -o mingw_dll.o
ar rcs libmingw_dll.a mingw_dll.o
~~~

使用静态库构建可执行程序：

~~~makefile
gcc -c use_mingw_dll.c -o use_mingw_dll.o
gcc -o use_mingw_dll.exe use_mingw_dll.o -L. -lmingw_dll
~~~

最后生成 use_mingw_dll.exe 可执行文件



### 3. 问题

(1) 使用 gcc 编译三个源文件main.c，x2x.c 和 x2y.c ，并生成一个动态库文件 .so ：

~~~makefile
gcc *.c -c -fpic
gcc *.o -shared -o liball.so
gcc -L.  -o my_program main.c -lall
export LD_LIBRARY_PATH=$PWD:$LD_LIBRARY_PATH
~~~

执行结果：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211017173733.png)



(2) 将 x2x.c 和 x2y.c 编译成 .a 静态库文件：

~~~makefile
gcc *.c -c -fpic
ar rcs liball.a x2x.o x2y.o
gcc -L. -o my_program main.c -lall
~~~

执行结果：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/1634464098268.png)



接下来比较使用静态库和动态库时可执行程序的大小：

动态库：

* my_program： 8320字节 
* liball.so：8040字节
* main.o：1480字节
* x2x.o：1600字节
* x2y.o：1536字节

静态库：

* my_program大小 8424字节（比起使用动态库编译的大了104个字节）
* liball.a：3352字节

* main.o：1480字节
* x2x.o：1600字节
* x2y.o：1530字节

由此可以看出，使用静态库的执行程序比起使用动态库的执行程序的体积要大。



### 4. ELF 文件格式

ELF 是 Executable and Linkable Format 的缩写，它用于可执行文件，可重定位目标文件，共享库和核心转储。

ELF 文件由两部分组成——ELF 头和文件数据。

### 5. 参考

[1] [MinGW 静态库和动态库](https://www.codeproject.com/Articles/84461/MinGW-Static-and-Dynamic-Libraries)





