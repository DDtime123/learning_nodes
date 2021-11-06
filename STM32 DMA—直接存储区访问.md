# STM32 DMA—直接存储区访问

### 1. DMA 简介

DMA——直接存储器存储，是一个外设。

DMA 控制器包括 DMA1 和 DMA2 ，其中 DMA1 具有 7 个通道，DMA2 具有 5 个通道，这里的通道是用来传递数据的。

DMA 的功能就是用来为其它外设传递数据提供通道。使用 DMA1 和 DMA2 时要使能 DMA1 或 DMA2 的时钟。

~~~c
// 使能 DMA1 时钟，DMA1 在 AHB 总线上
RCC_AHBPeriphClockCmd(RCC_AHBPeriph_DMA1, ENABLE);
~~~

### 2. DMA 的应用

外设要通过 DMA 的通道传输数据的步骤如下：

#### 2.1 发送DMA 请求

外设要先给 DMA 控制器（DMA1或DMA2）发送 DMA 请求。不同的请求对应着对应的 DMA 控制器上的不同通道：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211103162030.png)

#### 2.2 通道





DMA_InitTyleDef 结构体定义：

~~~c
typedef struct
{
	uint32_t DMA_PeripheralBaseAddr; // 外设地址
	uint32_t DMA_MemoryBaseAddr; 	// 存储器地址
	uint32_t DMA_DIR; 				// 传输方向
	uint32_t DMA_BufferSize; 		// 传输数目
	uint32_t DMA_PeripheralInc; 	// 外设地址增量模式
	uint32_t DMA_MemoryInc; 		// 存储器地址增量模式
	uint32_t DMA_PeripheralDataSize; // 外设数据宽度
	uint32_t DMA_MemoryDataSize; 	// 存储器数据宽度
	uint32_t DMA_Mode; 				// 模式选择
	uint32_t DMA_Priority; 			// 通道优先级
	uint32_t DMA_M2M; 				// 存储器到存储器模式
} DMA_InitTypeDef;
~~~



以下代码定义了对 DMA1 控制器的通道 5 或 6 的请求初始化。DMA1 控制器的通道 5 或 6 用来处理 USART 串口通信请求，所以在 初始化 DMA1 的 5 或 6通道后，马上将串口外设 USART 初始化并向控制器发送请求：

~~~c
#ifdef USE_STM3210C_EVAL
#define USARTy_DR_Address    0x40004404
#define USARTy_DMA1_Channel  DMA1_Channel6
#define USARTy_DMA1_IRQn     DMA1_Channel6_IRQn
#else
#define USARTy_DR_Address    0x40013804
#define USARTy_DMA1_Channel  DMA1_Channel5
#define USARTy_DMA1_IRQn     DMA1_Channel5_IRQn
#endif

...
void DMA_Configuration(void)
{
	DMA_InitTypeDef  DMA_InitStructure;

	/* USARTy_DMA1_Channel Config */
	DMA_DeInit(USARTy_DMA1_Channel);
	DMA_InitStructure.DMA_PeripheralBaseAddr = USARTy_DR_Address;
	DMA_InitStructure.DMA_MemoryBaseAddr = (uint32_t)DST_Buffer;
	DMA_InitStructure.DMA_DIR = DMA_DIR_PeripheralSRC;
	DMA_InitStructure.DMA_BufferSize = 10;
	DMA_InitStructure.DMA_PeripheralInc = 	DMA_PeripheralInc_Disable;
	DMA_InitStructure.DMA_MemoryInc = DMA_MemoryInc_Enable;
	DMA_InitStructure.DMA_PeripheralDataSize = DMA_PeripheralDataSize_HalfWord;
	DMA_InitStructure.DMA_MemoryDataSize = DMA_MemoryDataSize_HalfWord;
	DMA_InitStructure.DMA_Mode = DMA_Mode_Circular;
	DMA_InitStructure.DMA_Priority = DMA_Priority_High;
	DMA_InitStructure.DMA_M2M = DMA_M2M_Disable;
	DMA_Init(USARTy_DMA1_Channel, &DMA_InitStructure);

	/* Enable USARTy_DMA1_Channel Transfer complete interrupt */
	DMA_ITConfig(USARTy_DMA1_Channel, DMA_IT_TC, ENABLE);
 
	/* USARTy_DMA1_Channel enable */
	DMA_Cmd(USARTy_DMA1_Channel, ENABLE);
}
~~~

~~~c
/* EVAL_COM1 configuration ---------------------------------------------------*/
/* EVAL_COM1 configured as follow:
         - BaudRate = 115200 baud  
         - Word Length = 8 Bits
         - One Stop Bit
         - No parity
         - Hardware flow control disabled (RTS and CTS signals)
         - Receive and transmit enabled
*/
USART_InitStructure.USART_BaudRate = 115200;
USART_InitStructure.USART_WordLength = USART_WordLength_8b;
USART_InitStructure.USART_StopBits = USART_StopBits_1;
USART_InitStructure.USART_Parity = USART_Parity_No;
USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;
USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;

// 初始化串口函数(非标准库函数)
STM_EVAL_COMInit(COM1, &USART_InitStructure);
// 开启 USART 的 DMA 模式，一共有三个参数
// 参数一为 USART 或 UART 端口，可以是 USART1,2,3 或 UART 4,5
// 参数二为 DMA 请求的类别。USART_DMAReq_Tx 表示传输请求，USART_DMAReq_Rx 为接受请求
// 参数三为将设定的 DMA 状态：ENABLE 或 DISABLE
USART_DMACmd(EVAL_COM1, USART_DMAReq_Rx, ENABLE);
~~~

初始化 USART 端口：

~~~c
{
    GPIO_InitTypeDef GPIO_InitStructure;

    /* Enable GPIO clock */
    RCC_APB2PeriphClockCmd(COM_TX_PORT_CLK[COM] | COM_RX_PORT_CLK[COM] | RCC_APB2Periph_AFIO, ENABLE);

    /* Enable UART clock */
    if (COM == COM1)
    {
        RCC_APB2PeriphClockCmd(COM_USART_CLK[COM], ENABLE); 
    }
    else
    {
        /* Enable the USART2 Pins Software Remapping */
        GPIO_PinRemapConfig(GPIO_Remap_USART2, ENABLE);
        RCC_APB1PeriphClockCmd(COM_USART_CLK[COM], ENABLE);
    }

    /* Configure USART Tx as alternate function push-pull */
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_InitStructure.GPIO_Pin = COM_TX_PIN[COM];
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(COM_TX_PORT[COM], &GPIO_InitStructure);

    /* Configure USART Rx as input floating */
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
    GPIO_InitStructure.GPIO_Pin = COM_RX_PIN[COM];
    GPIO_Init(COM_RX_PORT[COM], &GPIO_InitStructure);

    /* USART configuration */
    USART_Init(COM_USART[COM], USART_InitStruct);

    /* Enable USART */
    USART_Cmd(COM_USART[COM], ENABLE);
}
~~~





