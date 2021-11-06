# STM32 外部中断

### 1. 系统异常和外部中断

外部中断器，也称为事件控制器，它通过对输入信号的上升/下降沿（即电位高低变化）的检测，实现对中断事件线的控制。

F103 在内核上搭载了一个异常响应系统，支持为数众多的系统异常和外部中断，其中系统异常有 8 个，外部中断 60 个。除了个别异常优先级锁死外，其余异常的优先级都可以被编程。

系统异常列表：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211103083713.png)

外部中断列表：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211103083759.png)

...

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211103083820.png)

### 2. NVIC

#### 2.1 NVIC 简介

NVIC 是嵌套向量中断控制器，控制着整个芯片中断相关的功能，它是内核中的一个外设。

#### 2.2 NVIC 结构体简介

在配置中断时，我们需要用到寄存器，NVIC 提供了一个结构体，其中包含了对许多寄存器的定义：

~~~c
// NVIC 结构体
typedef struct {
    __IO uint32_t ISER[8]; // 中断使能寄存器
    uint32_t RESERVED0[24];
    __IO uint32_t ICER[8]; // 中断清除寄存器
    uint32_t RSERVED1[24];
    __IO uint32_t ISPR[8]; // 中断使能悬起寄存器
    uint32_t RESERVED2[24];
    __IO uint32_t ICPR[8]; // 中断清除悬起寄存器
    uint32_t RESERVED3[24];
    __IO uint32_t IABR[8]; // 中断有效位寄存器
    uint32_t RESERVED4[56];
    __IO uint8_t IP[240]; // 中断优先级寄存器(8Bit wide)
    uint32_t RESERVED5[644];
    __O uint32_t STIR; // 软件触发中断寄存器
} NVIC_Type;
~~~

在配置中断时，一般只用 ISER，ICER 和 IP 三个寄存器，其中 ISER 用于使能中断，ICER 用于失能中断，IP 用于设置中断优先级。

#### 2.3 NVIC 中断配置固件库

~~~c
void NVIC_EnableIRQ(IRQn_Type IRQn); // 使能中断
void NVIC_DisableIRQ(IRQn_Type IRQn); // 失能中断
void NVIC_SetPendingIRQ(IRQn_Type IRQn); // 设置中断悬起位
void NVIC_ClearPendingIRQ(IRQn_Type IRQn); // 清除中断悬起位
uint32_t NVIC_GetPendingIRQ(IRQn_Type IRQn); // 获取悬起中断编号
void NVIC_SetPriority(IRQn_Type IRQn,uint32_t priority); // 设置中断优先级
uint32_t NVIC_GetPriority(IRQn_Type IRQn); // 获取中断优先级
void NVIC_SystemReset(void); // 系统复位
~~~

这些库函数我们在编程配置中断时基本不用，转而使用更为简洁的方法：

#### 2.4 优先级

#### 2.4.1 优先级定义

配置中断时，NVIC 结构体中就有对一个用于配置优先级的寄存器的定义——NVIC_IPRx，简称 IP 寄存器。IPR 寄存器宽度为 8 bits，可以表示 0 ~ 255，数值越小，优先级越高。由于 F103 中CM3芯片的精简化，F103 只用到了 4 bits 寄存器。

这 4 bits 的优先级寄存器定义了 抢占优先级和分组优先级。

##### 2.4.1.1 抢占优先级（主优先级）

当多个中断同时响应，抢占优先级高的就会抢占优先级低的的优先得到执行。

##### 2.4.1.2 子优先级

也是当多个中断同时响应时，如果抢占优先级相同，就根据子优先级高低决定中断的执行顺序。

#### 2.4.2 优先级分组

由县级的分组有外设 SCB 的应用程序中断和复位控制寄存器器 AIRCR 的 PRIGROUP 决定的。 

优先级分组真值表：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211103104311.png)

主优先级的数量决定了中断的阶级划分。如果存在两个主优先级，那么就有两个阶级，其中高优先级的中断必定优先于低优先级的中断执行。

优先级分组决定了在 IPRx 寄存器中怎么划分主优先级和子优先级的数量，即规定该程序中有几个阶级。每个程序中只有一个优先级分组。

#### 2.5 中断编程

我们使用如下结构体，可以简洁地配置中断：

~~~c
typedef struct{
    uint8_t NVIC_IRQChannel; // 中断源
	uint8_t NVIC_IRQChannelPreemptionPriority; // 抢占优先级
	uint8_t NVIC_IRQChannelSubPriority; // 子优先级
	FunctionalState NVIC_IRQChannelCmd; // 中断使能或者失能
} NVIC_InitTypeDef;
~~~

1) NVIC_IRQChannel：用来设置中断源 IRQn，IRQn_Type 结构体如下，其中包含了所有中断源：

~~~c
typedef enum IRQn {
	//Cortex-M3 处理器异常编号
	NonMaskableInt_IRQn = -14,
	MemoryManagement_IRQn = -12,
	BusFault_IRQn = -11,
	UsageFault_IRQn = -10,
    SVCall_IRQn = -5,
	DebugMonitor_IRQn = -4,
	PendSV_IRQn = -2,
	SysTick_IRQn = -1,
	//STM32 外部中断编号
	WWDG_IRQn = 0,
	PVD_IRQn = 1,
	TAMP_STAMP_IRQn = 2,
    
	// 限于篇幅，中间部分代码省略，具体的可查看库文件 stm32f10x.h

	DMA2_Channel2_IRQn = 57,
	DMA2_Channel3_IRQn = 58,
	DMA2_Channel4_5_IRQn = 59
} IRQn_Type;
~~~

2) NVIC_IRQChannelPreemptionPriority：抢占优先级，具体的值要根据优先级分组来确定。

3) NVIC_IRQChannelSubPriority：子优先级。具体的值根据优先级分组来确定。

4) NVIC_IRQChannelCmd：中断使能或者失能（enable或disable）。操作的是 NVIC_ISER 和 NVIC_ICER 寄存器。



##### 2.4.1 配置中断的步骤：

1) 使能外设某个中断，各个外设在寄存器中有相关的中断使能位。比如串口有发送完成中断，接收完成中断，这两个中断都由串口控制寄存器的相关中断使能位控制。

~~~c
// 使能 DMA1 时钟
// DMA1 外设在 AHB 总线上
/* Configure the system clocks */
RCC_Configuration();

// 使能 DMA 管道
/* Configures the DMA Channel */
DMA_Configuration();

// 配置 EVAL_COM1 端口
USART_InitStructure.USART_BaudRate = 115200;
USART_InitStructure.USART_WordLength = USART_WordLength_8b;
USART_InitStructure.USART_StopBits = USART_StopBits_1;
USART_InitStructure.USART_Parity = USART_Parity_No;
USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;
USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;

STM_EVAL_COMInit(COM1, &USART_InitStructure);
USART_DMACmd(EVAL_COM1, USART_DMAReq_Rx, ENABLE);
~~~

2) 初始化 NVIC_InitTypeDef 结构体，配置中断源，抢占优先级，子优先级以及使能或失能。

~~~c
#define USARTy_DMA1_IRQn     DMA1_Channel6_IRQn
...
// 配置应用程序的优先级分组
NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);

// 使能 USARTy_DMA1_IRQn 外设中断
/* Enable the USARTy_DMA1_IRQn Interrupt */
NVIC_InitStructure.NVIC_IRQChannel = USARTy_DMA1_IRQn;
NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0;
NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0;
NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
NVIC_Init(&NVIC_InitStructure);

// 使能  EXTI9_5 外设中断
/* Enable the EXTI9_5  Interrupt */
NVIC_InitStructure.NVIC_IRQChannel = EXTI9_5_IRQn;
NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;
NVIC_Init(&NVIC_InitStructure);
~~~



### 3. EXTI—外部中断/事件控制器

#### 3.1 EXTI 简介

EXTI 是外部中断/事件控制器，它管理了控制器的 20 个中断/事件线。每个事件线都有一个边沿检测器。EXTI 可以为每个中断/事件线配置为中断或者事件，以及触发事件的属性。

#### 3.2 中断/事件线

EXTI 有20个中断/事件线，每个GPIO都可以被设置为输入线，占用 EXTI0 至 EXTI15 ，另外还有七根用于特定的外设事件。

### 4. 外部中断 Led 实验

实验目的：配置中断函数，使得当一个GPIOA按键接高电平时，一个GPIOB Led 灯亮，当按键接低电平时，Led 灯灭。

首先进行 EXTI 中断源配置：

~~~c
//引脚定义
#define KEY1_INT_GPIO_PORT GPIOA
#define KEY1_INT_GPIO_CLK (RCC_APB2Periph_GPIOA | RCC_APB2Periph_AFIO)
#define KEY1_INT_GPIO_PIN GPIO_Pin_0
#define KEY1_INT_EXTI_PORTSOURCE GPIO_PortSourceGPIOA
#define KEY1_INT_EXTI_PINSOURCE GPIO_PinSource0
#define KEY1_INT_EXTI_LINE EXTI_Line0
#define KEY1_INT_EXTI_IRQ EXTI0_IRQn

#define KEY1_IRQHandler EXTI0_IRQHandler
~~~

~~~c
// 嵌套向量中断器配置
void NVIC_GPIO_Configuration()
{
	NVIC_InitTypeDef NVIC_InitStructure;

	/* 配置 NVIC 为优先级组 1 */
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);

	/* 配置中断源：按键 1 */
	NVIC_InitStructure.NVIC_IRQChannel = KEY1_INT_EXTI_IRQ;
	/* 配置抢占优先级：1 */
	NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;
	/* 配置子优先级：1 */
	NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;
	/* 使能中断通道 */
	NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
	NVIC_Init(&NVIC_InitStructure);
	
	/* 配置中断源：按键 2，其他使用上面相关配置 */
	NVIC_InitStructure.NVIC_IRQChannel = KEY2_INT_EXTI_IRQ;
	NVIC_Init(&NVIC_InitStructure);
}
~~~

~~~c
// 配置接收中断的按钮
void EXTI_Key_Config(void)
{
	GPIO_InitTypeDef GPIO_InitStructure;
	EXTI_InitTypeDef EXTI_InitStructure;
	
	/*开启按键 GPIO 口的时钟*/
	RCC_APB2PeriphClockCmd(KEY1_INT_GPIO_CLK,ENABLE);

	/* 配置 NVIC 中断*/
	NVIC_GPIO_Configuration();
	
	/*--------------------------KEY1 配置---------------------*/
	/* 选择按键用到的 GPIO */
	GPIO_InitStructure.GPIO_Pin = KEY1_INT_GPIO_PIN;
	/* 配置为浮空输入 */
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
	GPIO_Init(KEY1_INT_GPIO_PORT, &GPIO_InitStructure);

	/* 选择 EXTI 的信号源 */
	GPIO_EXTILineConfig(KEY1_INT_EXTI_PORTSOURCE, \
	KEY1_INT_EXTI_PINSOURCE);
	EXTI_InitStructure.EXTI_Line = KEY1_INT_EXTI_LINE;

	/* EXTI 为中断模式 */
	EXTI_InitStructure.EXTI_Mode = EXTI_Mode_Interrupt;
	/* 下降沿中断 */
	EXTI_InitStructure.EXTI_Trigger = EXTI_Trigger_Falling;
	/* 上升沿中断 */
	//EXTI_InitStructure.EXTI_Trigger = EXTI_Trigger_Rising;
	/* 使能中断 */
	EXTI_InitStructure.EXTI_LineCmd = ENABLE;
	EXTI_Init(&EXTI_InitStructure);
}
~~~

初始化输出 Led 灯：

~~~c
void led_init(void)
{
	// 使用 GPIO A3作为 输出端口
	GPIO_InitTypeDef GPIO_InitStructA;
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);  //使能GPIOA时钟 
	
	GPIO_InitStructA.GPIO_Mode    = GPIO_Mode_Out_PP;    
	GPIO_InitStructA.GPIO_Pin     = GPIO_Pin_3;           
	GPIO_InitStructA.GPIO_Speed   = GPIO_Speed_50MHz;    
	GPIO_Init(GPIOA,&GPIO_InitStructA);                   
}
~~~

配置中断函数：

~~~c
#define KEY1_IRQHandler EXTI0_IRQHandler

//GPIO 中断函数
void KEY1_IRQHandler(void)
{
	key_flag=1;
	//确保是否产生了 EXTI Line 中断
	if (EXTI_GetITStatus(KEY1_INT_EXTI_LINE) != RESET) {
		//清除中断标志位
		EXTI_ClearITPendingBit(KEY1_INT_EXTI_LINE);
		if(GPIO_ReadInputDataBit(GPIOA, GPIO_Pin_0) )
          {
            	// 如果读取A0位为高电平，就亮灯
                GPIO_SetBits(GPIOA, GPIO_Pin_3);
          }
          else
          {
              	// 如果读取A0位为低电平，就灭灯
				GPIO_ResetBits(GPIOA, GPIO_Pin_3);
          }
	}
	
}
~~~

### 5. 中断方式串口通信实验

串口 1 和 串口 2 引脚定义：

~~~c
// 串口 1 和 串口 2——USART1 和 USART2
USART_TypeDef* COM_USART[COMn] = {EVAL_COM1, EVAL_COM2}; 
// 串口输出端口
GPIO_TypeDef* COM_TX_PORT[COMn] = {EVAL_COM1_TX_GPIO_PORT, EVAL_COM2_TX_GPIO_PORT};
// 串口输入端口
GPIO_TypeDef* COM_RX_PORT[COMn] = {EVAL_COM1_RX_GPIO_PORT, EVAL_COM2_RX_GPIO_PORT};
// 两个串口的时钟
const uint32_t COM_USART_CLK[COMn] = {EVAL_COM1_CLK, EVAL_COM2_CLK};
// GPIO输出端口时钟
const uint32_t COM_TX_PORT_CLK[COMn] = {EVAL_COM1_TX_GPIO_CLK, EVAL_COM2_TX_GPIO_CLK};
// GPIO输入端口时钟
const uint32_t COM_RX_PORT_CLK[COMn] = {EVAL_COM1_RX_GPIO_CLK, EVAL_COM2_RX_GPIO_CLK};
// 输入引脚
const uint16_t COM_TX_PIN[COMn] = {EVAL_COM1_TX_PIN, EVAL_COM2_TX_PIN};
// 输出引脚
const uint16_t COM_RX_PIN[COMn] = {EVAL_COM1_RX_PIN, EVAL_COM2_RX_PIN};

USART_TypeDef* DEBUG_USARTx[COMn] = {USART1, USART2};
const uint32_t DEBUG_USART_CLK[COMn] = {RCC_APB2Periph_USART1, RCC_APB1Periph_USART2};
int DEBUG_USART_BAUDRATE[COMn] = {115200, 115200};
uint16_t DEBUG_USART_GPIO_CLK[COMn] = {RCC_APB2Periph_GPIOA, RCC_APB2Periph_GPIOA};
GPIO_TypeDef* DEBUG_USART_TX_GPIO_PORT[COMn] = {GPIOA, GPIOA};
uint16_t DEBUG_USART_TX_GPIO_PIN[COMn] = {GPIO_Pin_9, GPIO_Pin_2};
GPIO_TypeDef* DEBUG_USART_RX_GPIO_PORT[COMn] = {GPIOA, GPIOA};
uint16_t DEBUG_USART_RX_GPIO_PIN[COMn] = {GPIO_Pin_10, GPIO_Pin_3};
~~~

初始化串口配置：

~~~c
// 初始化串口配置
void STM_EVAL_COMInit(COM_TypeDef COM,USART_InitTypeDef* USART_InitStruct)
{
	GPIO_InitTypeDef GPIO_InitStructure;
	USART_InitTypeDef USART_InitStructure;

	if(COM == COM1){
		// 打开串口1 GPIO 的时钟
		RCC_APB2PeriphClockCmd(DEBUG_USART_GPIO_CLK[COM], ENABLE);
		// 打开串口1外设的时钟
		RCC_APB2PeriphClockCmd(DEBUG_USART_CLK[COM], ENABLE);
	}else{
		// 打开串口2 GPIO 的时钟
		RCC_APB2PeriphClockCmd(DEBUG_USART_GPIO_CLK[COM], ENABLE);
		// 打开串口2外设的时钟
		RCC_APB1PeriphClockCmd(DEBUG_USART_CLK[COM], ENABLE);
	}

	// 将 USART Tx 的 GPIO 配置为推挽复用模式
	GPIO_InitStructure.GPIO_Pin = DEBUG_USART_TX_GPIO_PIN[COM];
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(DEBUG_USART_TX_GPIO_PORT[COM], &GPIO_InitStructure);
	
	// 将 USART Rx 的 GPIO 配置为浮空输入模式
	GPIO_InitStructure.GPIO_Pin = DEBUG_USART_RX_GPIO_PIN[COM];
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
	GPIO_Init(DEBUG_USART_RX_GPIO_PORT[COM], &GPIO_InitStructure);

	// 配置串口的工作参数
	// 配置波特率
	USART_InitStructure.USART_BaudRate = DEBUG_USART_BAUDRATE[COM];
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
	USART_Init(DEBUG_USARTx[COM], &USART_InitStructure);

	// 串口中断优先级配置
	NVIC_Configuration(COM);

	// 使能串口接收中断
	USART_ITConfig(DEBUG_USARTx[COM], USART_IT_RXNE, ENABLE);

	// 使能串口
	USART_Cmd(DEBUG_USARTx[COM], ENABLE);
}
~~~

配置串口中断：

~~~c
void NVIC_Configuration(int COM)
{
	NVIC_InitTypeDef NVIC_InitStructure;

	/* 配置 NVIC 为优先级组 1 */
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);

	/* 配置中断源：串口1或2 */
	NVIC_InitStructure.NVIC_IRQChannel = DEBUG_USART_IRQ[COM];
	/* 配置抢占优先级：1 */
	NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;
	/* 配置子优先级：1 */
	NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;
	/* 使能中断通道 */
	NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
	NVIC_Init(&NVIC_InitStructure);
}
~~~

配置串口中断函数，此函数将串口输入的字符串获取并输出到串口输出：

~~~c
//串口中断函数
void USART1_IRQHandler(void)
{
	uint8_t ucTemp;
	if (USART_GetITStatus(USART1,USART_IT_RXNE)!=RESET) {
		ucTemp = USART_ReceiveData( USART1 );
		USART_SendData(USART1,ucTemp);
	}
}
~~~

结果：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/串口通信.gif)

### 6. 参考

[1] [零死角玩转STM32-F103指南者]