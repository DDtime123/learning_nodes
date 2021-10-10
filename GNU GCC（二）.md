# GNU GCC（二）

### 1. Linux 静态库和动态库

静态库平时作为单独文件存在，编译时被加载入执行文件中。

动态库或共享库在可执行文件之外单独存在。

静态库如果修改，可执行文件也需要修改，而动态库修改则不一定需要修改可执行文件。

使用静态库是每个应用程序之中都有库的一个副本

使用动态库时所有正在运行的应用程序都使用同一个库，无需每个应用程序都有副本，可执行程序大小更小。

#### 1.1 Linux 中创建动态库

首先将 .c 源文件做处理。由于多个程序可以使用动态库的同一实例，因此该库无法在固定地址存储数据，这是因为库在内存中的位置因程序而异。这是通过使用编译器标志 **-fpic** 来完成的。由于要在编译过程生成目标代码后应用此步骤，因此必须告诉编译器停止并且为每一个源文件返回一个目标文件 .o ，因此通过 -c 标志完成：

~~~makefile
gcc *.c -c -fpic
~~~

通过 -shared 标志编译所有 .o 文件。然后在稍后编译程序文件时，编译器会通过查找以 “lib” 开头并以库扩展名结尾（.so 动态库或 .a 静态库）的文件来识别库：

~~~makefile
gcc *.o -shared -o liball.so
~~~

将位置添加到环境变量 LD_LIBRARY_PATH 里：

~~~makefile
export LD_LIBRARY_PATH=$PWD:$LD_LIBRARY_PATH
~~~

#### 1.2 Linux 中创建静态库

首先使用相同的方式创建目标文件：

~~~makefile
gcc *.c -c -fpic
~~~

然后使用以下语句进行归档库：

~~~makefile
ar rcs liball.a *.o
~~~

程序编译：

-l 告诉编译器我们向包含库文件，-L 标志以及后边加的目录（这里是 . ）告诉编译器库文件在哪里，all告诉他寻找库 liball.so ，将 “lib” 和 “.so” 排除在标志外很重要，因为编译器会通过这种方式识别库文件：

~~~make
gcc -L. -lall -o my_program main.c
~~~



### 2. Windows 静态库和动态库

#### 2.1 Windows 中创建动态库

使用 Visual Studio 命令行工具创建

首先使用创建 .obj 对象文件：

~~~
cl /c /EHsc *.cpp
~~~

然后链接起来：

~~~makefile
cl /D_USRDLL /D_WINDLL <file-to-compile> <files-to-link> /link /DLL /OUT:<desired-dll-name>.dll
~~~

#### 2.2 Windows 中创建静态库

使用 Visual Studio 命令行程序构建静态库分两步。

首先，要使用相同的命令构建出一个 .obj 对象文件：

~~~makefile
cl /c /EHsc *.cpp
~~~

然后，使用如下命令链接代码并创建静态库 *.lib

~~~makefile
lib *.obj
~~~



