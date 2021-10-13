#  C 代码与汇编代码混合编程

#### 1. 在 C 代码中调用汇编程序代码

在 C 代码中调用汇编程序中函数的方法：

第一步，在 C 代码中声明函数的签名（包括函数名，参数列表和返回值），再在函数签名前添加 extern 表示该函数是在外部定义的：

~~~c
extern void func(void);
~~~

第二步，在汇编代码中 EXPORT 该函数：

~~~asm
	EXPORT func  ;1. EXPORT 函数
	
func   ;2. 实现函数体
	...
~~~



完整代码：

~~~c
#include <stdio.h>

extern int Init_1(int i);

int main(){
	int r = Init_1(100);
	return 0;
}
~~~

~~~asm
	AREA My_Function,CODE,READONLY
	EXPORT Init_1
		
Init_1

	MOV R1,#0
	MOV R2,#0
	
LOOP			; 循环10次
	CMP R1,#10
	BHS LOOP_END
	ADD R2,#1
	ADD R1,#1
	B LOOP
	
LOOP_END
	NOP
    
	END
~~~



#### 2. 在寄存器中传递参数

现在这里有一个 C 语言函数，一个在汇编语言中定义的函数 Init()，如果要使用在 C 语言函数中使用参数列表将参数传递到汇编函数中，再在汇编中使用这些参数值，就要了解参数列表以寄存器值的方式传递：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013092445.png)

主函数：

~~~c
#include <stdio.h>

extern int Init_1(int i);

int main(){
	int r = Init_1(100);
	return 0;
}
~~~

汇编函数，参数列表的第一个值存储在 R0 中：

~~~asm
	AREA My_Function,CODE,READONLY
	EXPORT Init_1
		
Init_1

	MOV R1,#0
	MOV R2,#0
	
LOOP
	CMP R1,R0 ; 使用传入参数 R0 作为循环标志，让循环达 100 次
	BHS LOOP_END
	ADD R2,#1
	ADD R1,#1
	B LOOP
	
LOOP_END
	NOP
	
	
	END
~~~

测试结果，成功地根据传入的参数执行了相应的代码：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/1634121471209.png)



#### 3. 获取汇编代码返回值

R0 寄存器不仅作为传递给子例程的参数列表中的第一个参数值，还作为结果返回给调用者。子例程代码执行完毕后，将结果存储到 R0 寄存器中，然后可以使用 BX LR 指令返回：

~~~asm
MOV R0,RESULT
BX LR
~~~

汇编代码更改如下：

~~~asm
	AREA My_Function,CODE,READONLY
	EXPORT Init_1
		
Init_1

	MOV R1,#0
	MOV R2,#0
	
LOOP
	CMP R1,R0
	BHS LOOP_END
	ADD R2,#1
	ADD R1,#1
	B LOOP
	
LOOP_END
	MOV R0,#12 ; R0 作为返回结果
	BX      lr ; BX lr 是返回的标志语句，lr 通常存储了返回地址
	
	END
~~~

测试结果，成功地将返回值取回到C代码中：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013185153.png)



#### 4. 在汇编代码中调用 C 语言函数

要在汇编代码中调用 C 语言代码中声明的函数，做法跟在 C 代码中调用汇编代码类似。

第一步，在 C 代码中声明函数，并声明为 extern ，这里的 extern 用于扩展函数的可见性，将函数的可见性扩展到整个程序：

~~~c
unsigned int l;

extern void func(void){
	l = 12;
}
~~~

第二步，在汇编代码中 IMPORT 函数，并且使用 bl func 就可以调用 C 函数：

~~~asm
	IMPORT func ; 引入函数
	
Init
	...
	bl func ; 调用 C 函数
	BX	lr  ; BX lr 是返回的标志语句，lr 通常存储了返回地址
~~~



第三步，lr 通常保存了返回地址。由于通常将 C 代码生成汇编代码的时候，会由编译器管理子程序调用时的符号，返回地址等信息的保存，而手动写汇编代码时则需要自己保存返回地址。

以下在 C 代码看来普遍的过程调用方法，在汇编中却不能正确运行，程序执行 BX lr 后会返回到 BL dummy 的下一句代码 MOV Ro,#12 前，从而形成死循环：

~~~asm
LOOP_END
	BL dummy   ; 调用了 C 语言中的函数
	MOV R0,#12 ; R0 作为返回结果
	BX      lr ; 执行后返回上一句，形成死循环
~~~

原因是：尽管 BL dummy 语句调用了 dummy 过程，但是在汇编语言中，当前过程的返回地址是由当前过程在调用其他过程之前自己存储起来的，所以调用了 dummy 函数后的 lr 中存储的是 dummy 函数的返回地址，请在调用其他过程前将 lr 的值使用寄存器保存起来，等待返回时再复原 lr：

~~~asm
LOOP_END
	MOV R8,lr  ; 保存起来 lr
	BL dummy   ; 调用了 C 语言中的函数
	MOV lr,R8 ; 复原 lr
	MOV R0,#12 ; R0 作为返回结果
	BX      lr ; BX lr 是返回的标志语句，lr 通常存储了返回地址
~~~

测试结果：成功地调用了 dummy 函数，并且返回了主函数：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013190331.png)



![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013190430.png)



#### 5. 参考

[1] [ASM 在寄存器中传递参数](https://bob.cs.sonoma.edu/IntroCompOrg-RPi/sec-reg-use.html)

[2] [子程序调用中的寄存器使用](https://www.keil.com/support/man/docs/armasm/armasm_dom1359731145503.htm)

[3] [从C和C++调用汇编函数](https://www.keil.com/support/man/docs/armclang_intro/armclang_intro_lmi1470147220260.htm)

[4] [如何从 ARM 程序中调用 C 函数](https://stackoverflow.com/questions/8422287/how-to-call-c-functions-from-arm-assembly)

[5] [bx lr 在 ARM 汇编语言里起什么作用](https://stackoverflow.com/questions/27084857/what-does-bx-lr-do-in-arm-assembly-language)