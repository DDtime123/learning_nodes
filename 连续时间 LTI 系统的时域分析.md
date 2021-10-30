# 连续时间 LTI 系统的时域分析

#### 1. Matlab 公式语法





(1) 使用 Matlab 求齐次微分方程 $y^{'''}(t) + 2y^{''}(t)+y^{'}(t)=0$ 的零输入响应，起始条件 $y(0_{-})=1$，$y^{'}(0_{-})=1$，$y^{''}(0_{-})=2$。

Matlab 程序：

~~~matlab
clear;clc;
eq = ‘D3y+D2y+Dy’;
cond = 'y{0}=1,Dy{0}=1,D2y{0}=2'
ans = dslove(eq,cond);
simplify(ans);
~~~

求 dsolve 函数：

~~~matlab

~~~

(2) 用 Matlab 产生单边衰减指数信号 $2e^{-1.5}\varepsilon(t)$

~~~matlab
clear;clc;
K = 2; a = -1.5;
t = 0:0.01:3 % 初始值0，步长0.01，终止量3
ft = K*exp(a*t); % 指数 exp()
plot(t,ft);grid on;
axis([0,3,0,2.2]);
title('单边指数衰减信号')
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211029193310.png)

(3) 用 Matlab 产生抽样信号 $Sa(t)=sin(t)/t$，定义为 $sinc(t)=sin({\pi}t)/{\pi}t$：

~~~matlab
% 范围 -6pi 至 6pi
clear;clc;
x = -6*pi:pi/100:6*pi;
y = sinc(x/pi);
plot(x,y);
grid on;
axis([-6*pi,6*pi,-1,1]);
title('抽样信号')
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211029194537.png)

(4) 矩形脉冲信号 ，使用 rectpuls 函数 $y=rectpuls(x,width)$，该函数产生一个幅度为1，宽度为width，且以 x=0 为对称轴的矩形脉冲信号，x 为自变量：

~~~matlab
clear;clc;
t = -0.5:0.01:3
t0=0.5;width=1;
y = 2*rectpuls(t-t0,width);
plot(t,y);
grid on;
axis([-0.5,3,-0.2,2.2]);
title('矩形脉冲信号')
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211029195802.png)

(5) 周期性矩形波信号 square 函数 $y=square(t,DUTY)$ ，该函数产生一个周期为 2π，幅值为 正负1的周期性方波信号，DUTY 表示占空比，即在一个周期内脉冲宽度（正值部分）和脉冲周期之比。默认占空比为 0.5.

产生周形方波信号使得频率 10Hz，占空比为 30%

~~~matlab
% 产生周形方波信号使得频率 10Hz，占空比为 30%
clear;clc;
t = 0:0.001:0.3
y = square(2*pi*10*t,30);
plot(t,y);grid on;
axis([0,0.3,-1.2,1.2]);
title('周期方波信号')
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211029200824.png)

(6) 单位阶跃信号 $y=\varepsilon(t)$

~~~matlab
t = -0.5:0.01:2
y = (t>=0)
plot(t,y);grid on;
axis([-0.5,2,-0.2,1.2]);
title('单位阶跃信号')
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211029201439.png)

(7) 门函数信号

~~~matlab
function f = uCT(t)
    f = (t>=0);
~~~

~~~matlab
t = -1:0.01:1
w = 0.5;
y = uCT(t+0.5)-uCT(t-0.5)
plot(t,y);grid on;
axis([-1,1,-0.2,1.2]);
title('门函数')
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211029202344.png)

#### 1.2 Matlab 定义函数

~~~matlab
% 返回值 f ，参数 t
funciton f = uCT(t)
	f = (t>=0);
~~~



