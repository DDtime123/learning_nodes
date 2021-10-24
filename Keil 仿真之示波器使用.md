# Keil 仿真之示波器使用

### 1. 示波器实验

#### 1.1 Keil 软件的虚拟示波器使用方法：

由于要用到 Keil 的虚拟仿真器，这里配置 Keil 的 Debug 设置，勾选 Use Simulator，Run to main()，将下方的 Dialog DLL 换成对应的参数，这里我用的芯片是 stm32f103c8 ，对应的参数就是 -pSTM32F103C8：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024152316.png)

#### 1.2 开始使用示波器

点击 Debug，打开调试界面，然后打开  Logic Analyzer：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024153355.png)

点击 Logic Anaylizer 窗口左上角的 Setup，就可以开始选择要查看信号的引脚了：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024154526.png)

这里使用 GPIOx_IDR.y 语法，其中 x 表示 A~G 端口，y 表示 0~15 号引脚，点击 Enter 后，Keil 会自动帮我们完成引脚地址设置：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024155014.png)

然后再将 Disaplay Type 设置为 Bit，再选好颜色，就可以点击运行了：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024155158.png)

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024155645.png)



查看波形图时可以使用鼠标滚轮放大或缩小图形间距，如果长时间看不到波形可以缩小图形间距查看。

值得注意的一点是要在 Keil 中编译设置里将外部晶振频率设置为 8Mhz，在库函数里 STM32F103 默认是 8Mhz。而有时候它会被自动设置为 12Mhz，这时要将它设置回来，否则在示波器里时间频率会不对：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024172808.png)

接下来比对波形的信息，我这里设置的是三个 Led 灯轮流闪烁，闪烁时长 1s，所以引脚的电平是 1s 高电平，2s 低电平，周期 3s：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024173308.png)

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024173335.png)

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211024173425.png)

比对结束，延迟稍有偏差，总体还是按照规律闪烁。



### 2. 参考

[1] [Keil5 仿真——示波器显示](https://www.jianshu.com/p/0fe2eddd315d)