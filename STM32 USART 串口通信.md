# STM32 USART 串口通信实验

**实验内容和任务**

1. 了解串口协议和RS-232标准，以及RS232电平与TTL电平的区别；了解\"USB/TTL转232\"模块（以CH340芯片模块为例）的工作原理。

2. 安装 stm32CubeMX，配合Keil，分别尝试使用寄存器地址方式（汇编或C，不限） 和HAL库这两种方式，完成下列任务：

   (1)重做上一个LED流水灯作业，即用GPIO端口完成3只LED红绿灯的周期闪烁。

   (2)完成一个STM32的USART串口通讯程序（查询方式即可，暂不要求采用中断方式），要求：1）设置波特率为115200，1位停止位，无校验位；2）STM32系统给上位机（win10）连续发送"hello windows！"。win10采用"串口助手"工具接收。

3. 在没有示波器条件下，可以使用Keil的软件仿真逻辑分析仪功能观察管脚的时序波形，更方便动态跟踪调试和定位代码故障点。 请用此功能观察第1题中3个GPIO端口的输出波形，和第2题中串口输出波形，并分析其正确与否。



### 1. RS-232串口通讯标准

#### 串口通讯简介

串口通讯是一种设备间常用的通信方式，对于串口通讯协议，可以将它按照通讯协议的通用分层思想分成物理层和协议层，其中物理层规定通讯系统中具有机械，电子功能部分的特性，确保原始数据在物理媒体中的传输；协议层规定通讯逻辑，统一收发双方的数据打包，解包标准。

RS232 通信协议是由 EIA（电子工业联盟）/TIA（电信工业协会）-232 在 1962 年开发的旧串行通信协议 

#### 1.1 物理层：

串口通讯的物理层标准有许多种，其中主要的一种是 RS-232 标准。RS-232 标准规定了信号的用途，通讯接口以及信号的电平标准。

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211023151141.png)

在以上的通讯方式中 RS-232 标准的电平经由DB9端口发送和接收后，通过电平转换芯片转换为 TTL 电平，由此电平信号才被控制器接受。

以下是 TTL 电平标准和 RS-232 电平标准：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211023165029.png)

控制器一般使用 TTL 的电平标准，所以常常会使用 MA3232 芯片对 TLL 以及 RS-232 电平的信号进行相互转化。



RS-232 标准是两个DB9接口之间传输的信号所遵循的标准，这里的 DB9 接口如下，是一种在老式的台式计算机中会有的接口，也叫做 COM 口：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211023153522.png)

这是 DB9 接口的 9 个信号引脚以及它们的功能，目前在其它工业控制使用的串口协议中，一般只使用 RXD，TXD 以及 GND 三条信号线，其它信号都已经被裁减掉了：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211023153734.png)

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211023154703.png)



#### 1.2 协议层

在串口通讯的协议层中，规定了数据包的内容，它由起始位，主体数据，校验位和停止位组成，下面是串口数据包的组成结构：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211023155508.png)

这里讲解异步串口通讯协议的数据包组成。

##### 1.2.1 波特率

由于在异步通讯中没有时钟信号，所以在两个通讯设备之间必须约定好波特率，即每个码元的长度，以便对信号进行解码，常用的波特率为 4800,9600,115200 等。

##### 1.2.2 通讯的起始和停止信号

串口通讯的一个数据包从起始信号开始，直到停止信号结束。数据包的起始位由一个逻辑0的数据位表示，而停止位可由0.5，1，1.5或2个逻辑1的数据位表示。

##### 1.2.3 有效数据

起始位的后面跟着的就是主体数据内容，也称为有效数据，有效数据的长度通常被约定为5,6,7或8位长。

##### 1.2.4 数据校验

在有效数据之后，有一个可选的校验位。 由于数据通信相对更容易受到外部干扰 导致传输数据出现偏差，可以在传输过程加上校验位来解决这个问题。校验方法有奇校验 (odd)、偶校验(even)、0 校验(space)、1 校验(mark)以及无校验(noparity)。

奇校验要求有效数据和校验位中“1”的个数为奇数，比如一个 8 位长的有效数据为： 01101001，此时总共有 4 个“1”，为达到奇校验效果，校验位为“1”，最后传输的数据 将是 8 位的有效数据加上 1 位的校验位总共 9 位。 

偶校验与奇校验要求刚好相反，要求帧数据和校验位中“1”的个数为偶数，比如数据 帧：11001010，此时数据帧“1”的个数为 4 个，所以偶校验位为“0”。 

0 校验是不管有效数据中的内容是什么，校验位总为“0”，1 校验是校验位总为“1”。 



 #### 1.3 RS232/USB转TTL模块

##### 1.3.1 RS232/USB转TTL模块

RS232转TTL模块主要实现RS232转串口和全双工串口转换为半双工串口信号的功能。

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024174128.png)

##### 1.3.2 USB转串口芯片:CH340

CH340是一个USB总线的转接芯片，实现USB转串口或者USB转打印口。
在串口方式下，CH340提供常用的MODEM联络信号，用于为计算机扩展异步串口，或者将普通的串口设备直接升级到USB总线。 

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024174821.png)



### 2. USART 收发通讯程序

#### 2.1 USART 同步异步收发器简介

USART是一个串行通信设备，可以进行全双工通信。

STM32f103c8 控制器有三个 USTRA 通信接口。我们通过这些通信接口连接电脑，在调试程序时就可以将一些调试信息 “打印” 在电脑端的串口调试助手上。

USART 接口通过三个引脚和其他设备连接在一起，任何 USART 双向通信至少需要两个脚：接收数据输入(RX)和发送数据输出(TX)。 

RX：接收数据串行输。通过过采样技术来区别数据和噪音，从而恢复数据。 

TX：发送数据输出。当发送器被禁止时，输出引脚恢复到它的 I/O 端口配置。当 发送器被激活，并且没东西发送时，TX 引脚处于高电平。在单线和智能卡模式 里，此 I/O 口被用来发送和接收数据。 



#### 2.2 USART 功能结构

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024105836.png)



#### 2.3 USART 通信编程

编程步骤：

* 使能 RX 和 TX 引脚GPIO 时钟和 USART 时钟
* 初始化 GPIO，并将 GPIO 复用到 USART 上
* 配置 USART 参数
* 配置中断控制器并使能 USART 接收中断
* 使能 USART
* 在 USART 接收中断服务函数时间数据接收和发送

##### 2.3.1 GPIO 和  USART 宏定义

第一步，GPIO 和  USART 宏定义：

~~~c
#define DEBUG_USARTx USART1
#define DEBUG_USART_CLK RCC_APB2Periph_USART1
#define DEBUG_USART_APBxClkCmd RCC_APB2PeriphClockCmd
#define DEBUG_USART_BAUDRATE 115200

#define DEBUG_USART_GPIO_CLK (RCC_APB2Periph_GPIOA)
#define DEBUG_USART_GPIO_APBxClkCmd RCC_APB2PeriphClockCmd
#define DEBUG_USART_TX_GPIO_PORT GPIOA
#define DEBUG_USART_TX_GPIO_PIN GPIO_Pin_9
#define DEBUG_USART_RX_GPIO_PORT GPIOA
#define DEBUG_USART_RX_GPIO_PIN GPIO_Pin_10
#define DEBUG_USART_IRQ USART1_IRQn
#define DEBUG_USART_IRQHandler USART1_IRQHandler
~~~

这里我们使用 USART1，选定 PA9 和 PA10 作为 USART 的 GPIO 发送和接受引脚。设定波特率为 115200。

##### 2.3.2 配置嵌套向量中断器 NVIC

第二步，配置嵌套向量中断器 NVIC：

~~~c
static void NVIC_Configuration(void)
{
	NVIC_InitTypeDef NVIC_InitStructure;
	/* 嵌套向量中断控制器组选择 */
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);
	/* 配置 USART 为中断源 */
	NVIC_InitStructure.NVIC_IRQChannel = DEBUG_USART_IRQ;
	/* 抢断优先级为 1 */
	NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;
	/* 子优先级为 1 */
	NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;
	/* 使能中断 */
	NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
	/* 初始化配置 NVIC */
	NVIC_Init(&NVIC_InitStructure);
}
~~~

##### 2.3.3 初始化 USART 端口

第三步，初始化 USART 端口：

这里遵循 GPIO 端口初始化的步骤：

* 先使能时钟。
* 定义初始化结构体，结构体是外设的初始化参数。
* 初始化结构体中的参数。
* 调用外设初始化函数。
* 初始化完成后，在主函数中调用初始化函数，再调用官方库函数，即可进行相应的操作。

以下是代码：

~~~c
void USART_Config(void)
{
	GPIO_InitTypeDef GPIO_InitStructure;
	USART_InitTypeDef USART_InitStructure;

	// 打开串口 GPIO 的时钟
	DEBUG_USART_GPIO_APBxClkCmd(DEBUG_USART_GPIO_CLK, ENABLE);
    
	// 打开串口外设的时钟
	DEBUG_USART_APBxClkCmd(DEBUG_USART_CLK, ENABLE);

	// 将 USART Tx 的 GPIO 配置为推挽复用模式
	GPIO_InitStructure.GPIO_Pin = DEBUG_USART_TX_GPIO_PIN;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(DEBUG_USART_TX_GPIO_PORT, &GPIO_InitStructure);

	// 将 USART Rx 的 GPIO 配置为浮空输入模式
	GPIO_InitStructure.GPIO_Pin = DEBUG_USART_RX_GPIO_PIN;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
	GPIO_Init(DEBUG_USART_RX_GPIO_PORT, &GPIO_InitStructure);

	// 配置串口的工作参数
	// 配置波特率
	USART_InitStructure.USART_BaudRate = DEBUG_USART_BAUDRATE;
	// 配置 针数据字长
	USART_InitStructure.USART_WordLength = USART_WordLength_8b;
	// 配置停止位
	USART_InitStructure.USART_StopBits = USART_StopBits_1;
	// 配置校验位
	USART_InitStructure.USART_Parity = USART_Parity_No ;
	// 配置硬件流控制
	USART_InitStructure.USART_HardwareFlowControl =
	USART_HardwareFlowControl_None;
	// 配置工作模式，收发一起
	USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;
	// 完成串口的初始化配置
	USART_Init(DEBUG_USARTx, &USART_InitStructure);

	// 串口中断优先级配置
	NVIC_Configuration();

	// 使能串口接收中断
	USART_ITConfig(DEBUG_USARTx, USART_IT_RXNE, ENABLE);
	
	// 使能串口
	USART_Cmd(DEBUG_USARTx, ENABLE);
}
~~~

对代码进行分析：

使用 GPIO_InitTypeDef 和 USART_InitTypeDef 结构体定义一个 GPIO 初始化变量以及一个 USART 初始化变量，这两个结构体内容如下：

~~~c
typedef struct {
	uint32_t USART_BaudRate; // 波特率
	uint16_t USART_WordLength; // 字长
	uint16_t USART_StopBits; // 停止位
	uint16_t USART_Parity; // 校验位
	uint16_t USART_Mode; // USART 模式
	uint16_t USART_HardwareFlowControl; // 硬件流控制
} USART_InitTypeDef;
~~~

~~~c
typedef struct
{
  uint16_t GPIO_Pin;             // 指定引脚
  GPIOSpeed_TypeDef GPIO_Speed;  // 输入/输出速率
  GPIOMode_TypeDef GPIO_Mode;	 // 输入/输出模式
}GPIO_InitTypeDef;
~~~

使能 GPIO 时钟，同时使能 USART 外设时钟。这里初始化时钟使用的是 RCC_APB2PeriphClockCmd 函数。下面的的 DEBUG_USART_GPIO_APBxClkCmd 以及 DEBUG_USART_APBxClkCmd 实际上都是我们给 RCC_APB2PeriphClockCmd 设置的宏：

~~~c
// 宏定义
#define DEBUG_USART_GPIO_APBxClkCmd RCC_APB2PeriphClockCmd
#define DEBUG_USART_APBxClkCmd RCC_APB2PeriphClockCmd
...
// 打开串口 GPIO 的时钟
DEBUG_USART_GPIO_APBxClkCmd(DEBUG_USART_GPIO_CLK, ENABLE);
// 打开串口外设的时钟
DEBUG_USART_APBxClkCmd(DEBUG_USART_CLK, ENABLE);
~~~

然后是设置工作模式：

~~~c
// 将 USART Tx 的 GPIO 配置为推挽复用模式
	GPIO_InitStructure.GPIO_Pin = DEBUG_USART_TX_GPIO_PIN;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(DEBUG_USART_TX_GPIO_PORT, &GPIO_InitStructure);

	// 将 USART Rx 的 GPIO 配置为浮空输入模式
	GPIO_InitStructure.GPIO_Pin = DEBUG_USART_RX_GPIO_PIN;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
	GPIO_Init(DEBUG_USART_RX_GPIO_PORT, &GPIO_InitStructure);
~~~

因为使用 USART 同步异步串口通讯方式，要设置串口的工作参数，波特率设置为 115200，字长为 8 ，1 个停止位，没有校验位，不使用硬件流控制，收发一体工作模式：

~~~c
	// 配置串口的工作参数
	// 配置波特率
	USART_InitStructure.USART_BaudRate = DEBUG_USART_BAUDRATE;
	// 配置 针数据字长
	USART_InitStructure.USART_WordLength = USART_WordLength_8b;
	// 配置停止位
	USART_InitStructure.USART_StopBits = USART_StopBits_1;
	// 配置校验位
	USART_InitStructure.USART_Parity = USART_Parity_No ;
	// 配置硬件流控制
	USART_InitStructure.USART_HardwareFlowControl =
	USART_HardwareFlowControl_None;
	// 配置工作模式，收发一起
	USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;
	// 完成串口的初始化配置
	USART_Init(DEBUG_USARTx, &USART_InitStructure);
~~~

程序用到 USART 接受中断，需要配置 NVIC， 这里调用 NVIC_Configuration 函数完成 配置。配置完 NVIC 之后调用 USART_ITConfig 函数使能 USART 接收中断。 最后调用 USART_Cmd 函数使能 USART，这个函数最终配置的是 USART_CR1 的 UE 位，具体的作用是开启 USART 的工作时钟，没有时钟那 USART 这个外设自然就工作不了。 

~~~c
// 串口中断优先级配置
NVIC_Configuration();

// 使能串口接收中断
USART_ITConfig(DEBUG_USARTx, USART_IT_RXNE, ENABLE);
	
// 使能串口
USART_Cmd(DEBUG_USARTx, ENABLE);
~~~

##### 2.3.4 发送字符

~~~c
/***************** 发送一个字符 **********************/
void Usart_SendByte( USART_TypeDef * pUSARTx, uint8_t ch)
{
	/* 发送一个字节数据到 USART */
	USART_SendData(pUSARTx,ch);

	/* 等待发送数据寄存器为空 */
	while (USART_GetFlagStatus(pUSARTx, USART_FLAG_TXE) == RESET);
}

/***************** 发送字符串 **********************/
void Usart_SendString( USART_TypeDef * pUSARTx, char *str)
{
	unsigned int k=0;
	do {
		Usart_SendByte( pUSARTx, *(str + k) );
	k++;
	} while (*(str + k)!='\0');
    
	/* 等待发送完成 */
	while (USART_GetFlagStatus(pUSARTx,USART_FLAG_TC)==RESET) {
	}
}
~~~

Usart_SendByte 函数用于指定从哪个 USART 端口发送一个 ASCII 码字符。它接收两个参数，第一个为 USART端口编号，第二个为 ASCII 字符，它通过调用标准库函数 USART_SendData 来实现，且使用 USART_GetFlagStatus 函数增加了等待发送完成功能。USART_GetFlagStatus 函数原型如下：

~~~c
FlagStatus USART_GetFlagStatus(USART_TypeDef* USARTx, uint16_t USART_FLAG);
~~~

它接收两个参数，第一个是 USART 端口信息，第二个是事件标志。这里我们循环检测数据寄存器为空这个事件，一旦数据寄存器为空，就跳出循环。

Usart_SendString 函数通过循环调用 Usart_SendByte 函数发送字符串。

##### 2.3.5 主函数

接下来是主函数，我们让它循环发送信息，中间间隔 1s：

~~~c
int main(void)
{
	USART_Config();
	
	while(1){
		Usart_SendString( DEBUG_USARTx,"Hello STM32!\n");
		delay_ms(1000);
	}
}
~~~

##### 2.3.6 程序运行

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/动画5.gif)



### 3. 参考

[1] [stm32 GPIO简单介绍及初始化配置（库函数）](https://blog.csdn.net/asdfg1075511750/article/details/79663568)

[2] [STM32延时函数的三种方法](https://blog.csdn.net/qqGHJ/article/details/81429100?ops_request_misc=&request_id=&biz_id=102&utm_term=stm32%E5%BB%B6%E6%97%B6%E5%87%BD%E6%95%B0delay&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-81429100.nonecase&spm=1018.2226.3001.4187)

[3] [零死角玩转STM32—F103指南者]

