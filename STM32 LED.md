# STM32 LED实验

**STM32 引脚**

GND：ground 接地

TXD： = TX 发送

RXD： = RX 接收

TXL： = TX LED = 传输正在进行的 LED

RXL：= RX LED = 接收正在进行的 LED



**GPIO 端口简介**

通用目的输入输出端口，是微控制器中最简单也是最常用的外设。由于芯片封装不同，最多拥有 GPIOA，GPIOB...GPIOG 等7组端口，每组端口最多拥有 Pin0~Pin15 共16个引脚。根据连接对象不同，GPIO 端口的每一个引脚可以独立设置成不同的工作模式。

**GPIO 的工作模式和典型应用场景**

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211020081252.png)

**GPIO 端口编程涉及的标准外设库函数**

* RCC_APB2eriphClockCmd()：控制 GPIO 的时钟
* GPIO_Init()：初始化配置GPIO
* GPIO_SetBits()：将指定 GPIO 引脚设置为高电平
* GPIO_ResetBits()：将指定 GPIO 引脚设置为低电平
* GPIO_ReadInputDataBit()：读取指定 GPIO 引脚电平



#### 1. GPIO端口初始化设置步骤

* 先使能时钟。

* 定义初始化结构体，结构体是外设的初始化参数。

* 初始化结构体中的参数。

* 调用外设初始化函数。

* 初始化完成后，在主函数中调用初始化函数，再调用官方库函数，即可进行相应的操作。



STM32f103c8t6 最小系统板：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211020092350.png)



#### 1. 利用串口下载程序

将系统启动模式设置为从系统存储器启动：即将 BOOT1 接地，BOOT0 接电源 3.3V。

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211020082023.png)

然后使用 mcuisp 或 Flymcu 等软件将 hex 程序下载到板上。



下载完成后再将启动模式修改回来，即将 BOOT1 和 BOOT0 都接地：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211020092624.png)

最后再按下板上的 Reset 键就可以启动程序。



#### 2.**使用 Keil 创建工程项目：**

使能GPIO时钟：

~~~c
GPIO_InitTypeDef GPIO_InitStruct;                    //定义初始化结构体
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE); //使能GPIOA时钟
~~~

配置GPIO工作模式和IO属性：

~~~c
GPIO_InitStruct.GPIO_Mode    = GPIO_Mode_Out_PP;     //配置模式
GPIO_InitStruct.GPIO_Pin     = GPIO_Pin_0;           //配置哪个IO口
GPIO_InitStruct.GPIO_Speed   = GPIO_Speed_50MHz;     //配置IO口速度,仅输出有效
~~~

初始化GPIO的参数：

~~~c
GPIO_Init(GPIOA,&GPIO_InitStruct);                   //初始化GPIOA的参数为以上结构体
~~~

完整代码：

~~~c
void led_init(void)
{
	GPIO_InitTypeDef GPIO_InitStruct;                    //定义初始化结构体
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE); //使能GPIOA时钟
	
	GPIO_InitStruct.GPIO_Mode    = GPIO_Mode_Out_PP;     //配置模式
	GPIO_InitStruct.GPIO_Pin     = GPIO_Pin_0;           //配置哪个IO口
	GPIO_InitStruct.GPIO_Speed   = GPIO_Speed_50MHz;     //配置IO口速度,仅输出有效
	GPIO_Init(GPIOA,&GPIO_InitStruct);                   //初始化GPIOA的参数为以上结构体
}
~~~

建立延迟函数：

~~~c
volatile unsigned long time_delay; // 延时时间，注意定义为全局变量
//延时n_ms
void delay_ms(volatile unsigned long nms)
{
    //SYSTICK分频--1ms的系统时钟中断
    if (SysTick_Config(SystemFrequency/1000))
    {
   
        while (1);
    }
    time_delay=nms;//读取定时时间
    while(time_delay);
    SysTick->CTRL=0x00; //关闭计数器
    SysTick->VAL =0X00; //清空计数器
}
//延时nus
void delay_us(volatile unsigned long nus)
{
 //SYSTICK分频--1us的系统时钟中断
    if (SysTick_Config(SystemFrequency/1000000))
    {
   
        while (1);
    }
    time_delay=nus;//读取定时时间
    while(time_delay);
    SysTick->CTRL=0x00; //关闭计数器
    SysTick->VAL =0X00; //清空计数器
}
 
    //在中断中将time_delay递减。实现延时
 
void SysTick_Handler(void)
{
    if(time_delay)
        time_delay--;
}
~~~

最后在主函数里将三个GPIO信号循环即可：

~~~c
int main(void)
{
	led_init();				
	
	while(1){
		GPIO_SetBits(GPIOA,GPIO_Pin_4);
		GPIO_ResetBits(GPIOB,GPIO_Pin_10);
		GPIO_ResetBits(GPIOC,GPIO_Pin_13);
		delay_ms(1000);
		
		
		GPIO_ResetBits(GPIOA,GPIO_Pin_4);
		GPIO_SetBits(GPIOB,GPIO_Pin_10);
		GPIO_ResetBits(GPIOC,GPIO_Pin_13);
		delay_ms(1000);
		
		GPIO_ResetBits(GPIOA,GPIO_Pin_4);
		GPIO_ResetBits(GPIOB,GPIO_Pin_10);
		GPIO_SetBits(GPIOC,GPIO_Pin_13);
		delay_ms(1000);
	}
}
~~~





使用寄存器：

~~~c
// 外设基地址
#define PERIPH_BASE ((unsigned int)0x40000000)
// 总线基地址
#define APB2PERIPH_BASE (PERIPH_BASE + 0x10000)
// 以下是GPIOA端口相关地址
// GPIOA基地址
#define GPIOA_BASE (APB2PERIPH_BASE + 0x0800)
// GPIOA的7个寄存器的地址
#define GPIOA_CRL *(unsigned int*)(GPIOA_BASE+0x00)
#define GPIOA_CRH *(unsigned int*)(GPIOA_BASE+0x04)
#define GPIOA_IDR *(unsigned int*)(GPIOA_BASE+0x08)
#define GPIOA_ODR *(unsigned int*)(GPIOA_BASE+0x0C)
#define GPIOA_BSRR *(unsigned int*)(GPIOA_BASE+0x10)
#define GPIOA_BRR *(unsigned int*)(GPIOA_BASE+0x14)
#define GPIOA_LCKR *(unsigned int*)(GPIOA_BASE+0x18)
// GPIOB端口相关地址
#define GPIOB_BASE (APB2PERIPH_BASE + 0x0C00)
#define GPIOB_CRL *(unsigned int*)(GPIOB_BASE+0x00)
#define GPIOB_CRH *(unsigned int*)(GPIOB_BASE+0x04)
#define GPIOB_IDR *(unsigned int*)(GPIOB_BASE+0x08)
#define GPIOB_ODR *(unsigned int*)(GPIOB_BASE+0x0C)
#define GPIOB_BSRR *(unsigned int*)(GPIOB_BASE+0x10)
#define GPIOB_BRR *(unsigned int*)(GPIOB_BASE+0x14)
#define GPIOB_LCKR *(unsigned int*)(GPIOB_BASE+0x18)
// GPIOC端口相关地址
#define GPIOC_BCSE (APB2PERIPH_BASE + 0x1000)
#define GPIOC_CRL *(unsigned int*)(GPIOC_BASE+0x00)
#define GPIOC_CRH *(unsigned int*)(GPIOC_BASE+0x04)
#define GPIOC_IDR *(unsigned int*)(GPIOC_BASE+0x08)
#define GPIOC_ODR *(unsigned int*)(GPIOC_BASE+0x0C)
#define GPIOC_BSRR *(unsigned int*)(GPIOC_BASE+0x10)
#define GPIOC_BRR *(unsigned int*)(GPIOC_BASE+0x14)
#define GPIOC_LCKR *(unsigned int*)(GPIOC_BASE+0x18)
// RCC 时钟基地址
#define RCC_BASE (AHBPERIPH_BASE + 0x1000)
// RCC 使能时钟基地址
#define RCC_APB2ENR *(unsigned int*)(RCC_BASE+0x18)
~~~



第一步，为了使用寄存器控制GPIO端口产生高低电平，首先得将对应的 GPIO A~G 时钟使能，在这里使用到了RCC 使能时钟基地址 RCC_APB2ENR：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211020184806.png)

~~~c
RCC_APB2ENR |= (1<<3); 			// 使能 GPIOB 的时钟
~~~

第二步，为了使用该 GPIO 的引脚用于输出信号，还要设置 GPIO 引脚的工作模式，设置 GPIO 端口的工作方式使用 GPIOx_CRL 和 GPIOx_CRH 寄存器，这两个寄存器分别控制 GPIO 端口的 0~7 号（Low）引脚和 8~15（High）号引脚的输入输出模式。

这里将 **GPIOB** 端口的 **0** 号引脚设置为**推挽输出** （GPIO_Mode_Out_PP）模式：

~~~c
GPIOB_CRL &= ~( 0x0F<< (4*0));  // 清空 PB0 管脚
GPIOB_CRL |= (10<<4*0);  		// 设置 PB0 管脚为推挽输出模式，且速率设置为2MHz
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211020190637.png)

GPIOx_CRL 包含 0~7 号引脚，如上图中橙色的部分 CNF0， MODE0显然表示用来控制 0 号引脚的输入输出模式的；

CNFx，MODEx （x从0~15）的不同组合对应的 8 种工作模式的规则是：当 MODEx 为 00 时，CNFX 的值对应的 x 号引脚工作模式：

* 00：模拟输入模式
* 01：浮空输入模式
* 10：上拉/下拉输入模式
* 11：保留

MODEx > 00 时：

* 00：通用推挽输出模式
* 01：通用开漏输出模式
* 10：复用功能推挽输出模式
* 11：复用功能开漏输出模式

MODEx 的不同值表示不同的输入输出速度：

* 00：输入模式（复位后的状态）
* 01：输出模式，最大速度 10MHz
* 10：输出模式，最大速度 2MHz
* 11：输出模式，最大速度 50MHz

我这里首先使用如下公式清空了 PB0 的引脚，其中x可用 0~7 ，表示 GPIOx_CRL 或 GPIOx_CRH 的 8 个引脚：

~~~c
GPIOB_CRL &= ~( 0x0F<< (4*x));  // 清空 PBx 管脚
~~~

然后将 PB0 设置为了推挽输出模式，其中的 10 是MODEx 的值，表示最大速率 2 MHz：

~~~c
GPIOB_CRL |= (10<<4*x);  		// 设置 PBx 管脚为推挽输出模式，且速率设置为2MHz
~~~

以下是用来控制输入输出模式的快捷变量：

~~~c
typedef enum
{ GPIO_Mode_AIN = 0x0, 			// 模拟输入 0000 0000
  GPIO_Mode_IN_FLOATING = 0x04, // 浮空输入 0000 0100
  GPIO_Mode_IPD = 0x28,			// 下拉输入 0010 1000
  GPIO_Mode_IPU = 0x48,			// 上拉输入 0100 1000
 
  GPIO_Mode_Out_OD = 0x14,		// 开漏输出 0001 0100
  GPIO_Mode_Out_PP = 0x10,		// 推挽输出 0001 0000
  GPIO_Mode_AF_OD = 0x1C,		// 复用开漏输出 0001 1100
  GPIO_Mode_AF_PP = 0x18		// 复用推挽输出 0001 1000
}GPIOMode_TypeDef;		
~~~

GPIOMode_TypeDef 中的枚举量的低4位中只用到了高2位，而高4位用于表示一些标志（ 输入/输出/下拉输入/上拉输入模式 ）。



第三步，控制对应的端口位产生高低电平，我这里使用端口位设置/清除寄存器 GPIOx_BSRR，每个 GPIO(A~F) 端口都具有一个 GPIOx_BSRR 寄存器。

~~~c
GPIOB_BSRR = 0x01<<0;    // PB0 设置为高电平
~~~

GPIOx_BSRR 这个 32 位寄存器的每一位用于控制 GPIOx 端口的 16 个管脚的高/低电平状态：如下图所示，低 16 位中，BSx（x取0~15）置 1 ，则将该 GPIO 端口的 x 号管脚置为高电平；高 16 位中，BRx（x取0~15）置 1 ，则将该 GPIO 端口的 x 号管脚置为低电平。

并且 BSx 只在 **为1** 时对置为高电平起作用，在**为0**时不对高低电平起作用，BRx 类似，所以可以在使用了以下赋值语句给 GPIOB_BSRR 赋值后继续赋值，将其它位同时设置为高电平，而不改变已有位的高电平状态：

~~~c
GPIOB_BSRR = 0x01<<0;    // PB0 设置为高电平
GPIOB_BSRR = 0x01<<1;    // PB1 设置为高电平，同时 PB0 仍为高电平
~~~



![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211020183436.png)



使用寄存器设置GPIO端口以及流水灯代码如下：

~~~c
// 使能GPIO以及设置引脚工作模式
void led_init2(void){
	RCC_APB2ENR |= (1<<2); // 使能 GPIOA
	RCC_APB2ENR |= (1<<3); // 使能 GPIOB
	RCC_APB2ENR |= (1<<4); // 使能 GPIOC
	
	GPIOB_CRL &= ~( 0x0F<< (4*0)); // 清空 PB0
	GPIOB_CRL |= (10<<4*0);  // 设置 PB0 管脚为推挽输出模式，且速率设置为2MHz

	GPIOA_CRL &= ~( 0x0F<< (4*3)); // 清空 PA3
	GPIOA_CRL |= (10<<4*3);  // 设置 PA3 管脚为推挽输出模式，且速率设置为2MHz
	
	GPIOC_CRH &= ~( 0x0F<< (4*5)); // 清空 PC13
	GPIOC_CRH |= (10<<4*5);  // 设置 PC13 管脚为推挽输出模式，且速率设置为2MHz
}
~~~

~~~c
void state1(){
		GPIOB_BSRR = 0x01<<0;    		// PB0 高电平
		GPIOA_BSRR = 0x01<<(16+3);    // PA3 低电平
		GPIOC_BSRR = 0x01<<(16+13);    // PC13 低电平
}

void state2(){
		GPIOB_BSRR = 0x01<<(16+0);    // PB0 低电平
		GPIOA_BSRR = 0x01<<3;    		// PA3 高电平
		GPIOC_BSRR = 0x01<<(16+13);    // 低电平
}

void state3(){
		GPIOB_BSRR = 0x01<<(16+0);     // PB0 低电平
		GPIOA_BSRR = 0x01<<(16+3);    // PA3 低电平
		GPIOC_BSRR = 0x01<<13;    	  // PC13 高电平
}
~~~

~~~c
// 主函数
unsigned int i=0;
int main(void)
{
	led_init2();	
	while(1){
		switch(i%3){
			case 0: state1();
				break;
			case 1: state2();
				break;
			case 2: state3();
				break;
		}
		delay_ms(1000);
		i++;
	}
}
~~~

执行效果：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/动画4.gif)



#### 3. **利用 STM32CubeMX 创建工程项目：**

第一步，在 STM32CubeMX  里将引脚 PA3，PB10，PC13设置为 推挽输出模式：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211019210243.png)

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211020211437.png)

点击 Generate Code ，选择对应的 IDE 后就可以生成该工程的工程项目：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211020211558.png)

使用 STM32CubeMX  生成的程序使用的是 HAL 库。

将主函数里的循环程序修改如下，即可产生流水线Led灯：

~~~c
while(1){
	HAL_GPIO_WritePin(Led_GPIO_Port,Led_Pin,GPIO_PIN_SET);
	HAL_GPIO_WritePin(Led2_GPIO_Port,Led2_Pin,GPIO_PIN_RESET);	  HAL_GPIO_WritePin(Led3_GPIO_Port,Led3_Pin,GPIO_PIN_RESET);
	HAL_Delay(1000);
		
	HAL_GPIO_WritePin(Led_GPIO_Port,Led_Pin,GPIO_PIN_RESET);	HAL_GPIO_WritePin(Led2_GPIO_Port,Led2_Pin,GPIO_PIN_SET);			
    HAL_GPIO_WritePin(Led3_GPIO_Port,Led3_Pin,GPIO_PIN_RESET);
	HAL_Delay(1000);
	
	HAL_GPIO_WritePin(Led_GPIO_Port,Led_Pin,GPIO_PIN_RESET);	HAL_GPIO_WritePin(Led2_GPIO_Port,Led2_Pin,GPIO_PIN_RESET);
	HAL_GPIO_WritePin(Led3_GPIO_Port,Led3_Pin,GPIO_PIN_SET);
	HAL_Delay(1000);
}
~~~

#### 4. 参考

[1] [stm32 GPIO简单介绍及初始化配置（库函数）](https://blog.csdn.net/asdfg1075511750/article/details/79663568)

[2] [STM32延时函数的三种方法](https://blog.csdn.net/qqGHJ/article/details/81429100?ops_request_misc=&request_id=&biz_id=102&utm_term=stm32%E5%BB%B6%E6%97%B6%E5%87%BD%E6%95%B0delay&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-81429100.nonecase&spm=1018.2226.3001.4187)