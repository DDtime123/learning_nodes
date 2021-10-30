# Matlab 使用

### 1. 图像绘制

~~~matlab
clear;%清除之前所有已有的变量
x=0:0.01*pi:2*pi;%自变量x采样，区间为(0,2π)，采样间隔为0.01π
y=x.^2+sin(2*x).*x;%符号需要用点，MATLAB中*为叉乘，.*为点乘
plot(x,y);%plot函数绘制图像
xlabel('X-Axis');
ylabel('Y-Axis');
title('y=x^2+sin(2*x)*x函数图像');
~~~

一个坐标区域多图像绘制

~~~matlab
clear;%清除之前所有已有的变量
x=0:0.01*pi:2*pi;%自变量x采样，区间为(0,2π)，采样间隔为0.01π
y1=x.^2+sin(2*x).*x;%符号需要用点，MATLAB中*为叉乘，.*为点乘
y2=cos(2*x).*x;
plot(x,y1);%plot函数绘制图像
hold on;
plot(x,y2);
xlabel('X-Axis');
ylabel('Y-Axis');
title('函数图像');
~~~

### 2. Matlab 语法

: 冒号运算符作用——产生行向量

* e1:e2:e3 其中 e1 为初始值，e2位步长，e3为终止量，例如 t = 0:1:5 将产生 t=[0 1 2 3 4 5]
* 也可以只有两个参数 e1,e3 ，此时默认步长为1

还可以利用 linspace(a,b,n) 产生行向量

~~~matlab
linspace(a,b,n)
~~~

其中 a ，b 为行向量的第一个和最后一个元素，n 是元素总数，省略n自动生成100个元素。

### 3. Matlab 信号分析z

#### 3.1 单位冲激信号

~~~matlab
k = -10 : 10; %自变量为k
yd = (k == 0); % 仅当k=0时有强度
stem(k, yd);

xlabel('k');
ylabel('f[k]');
title('unit impulse signal');
~~~

stem 用来画离散信号图，参数一表示横轴刻度，参数二是数据序列。

plot 用来画连续信号图

#### 3.2 单位阶跃信号

~~~matlab
t = -10 : 0.01 : 10;
ya = stepfun(t, 0);
plot(t, ya);

hold on;

k = -10 : 10;
yd = stepfun(k, 0);
stem(k, yd);

xlabel('t / k');
ylabel('f(t) / f[k]');
title('unit step signal');
~~~



#### 3.3 正弦信号

~~~matlab
t = -10 : 0.01 : 10;
ya = sin(t);
plot(t, ya);

hold on;

k = -10 : 10;
yd = sin(k);
stem(k, yd);

xlabel('t / k');
ylabel('f(t) / f[k]');
title('sinusoidal signal');
~~~