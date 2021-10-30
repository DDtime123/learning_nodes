# Python 多元线性回归

### 1. 多元线性回归

多元线性回归指具有两个或多个自变量的线性回归的情况。

如果只有两个自变量，估计的回归函数是 $f(x1,x2)=b_0+b_1x_1+b_2x_2$。它表示三维空间中的回归平面。回归的目标是确定权重 $b_0,b_1 和 b_2$ 的值，使得该平面尽可能接近实际响应并产生最小的 SSR（残差平方和）。



#### 1.1 回归模型假设

* 线性：因变量和自变量之间的关系应该是线性的
* 同方差性：应保持误差的恒定方差
* 多元正态性：多元回归假设残差呈正态分布
* 缺乏或没有多重共线性：假设数据中很少或没有多重共线性

虚拟变量：

这里我们使用了一个分类数据 style，它将非数值数据转换成了数值数据并添加到回归模型中。

自变量和因变量：

这里的因变量是 price，其余属性是自变量：

因变量：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211029082614.png)

自变量：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211029082651.png)

显著性水平：







#### 1.2 拆分数据

将数据拆分为训练集和测试集，这里使用 sklearn 库进行训练。

~~~python
from from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test =  train_test_split(X,y,test_size = 0.2, random_state= 0)
~~~

#### 1.3 导入线性回归模型

~~~python
from sklearn.linear_model import LinearRegression 
#创建一个LinearRegression对象class 
LR = LinearRegression() 
#训练数据
LR.fit(x_train,y_train)
~~~

#### 1.4 预测收益

~~~python
y_prediction = LR.predict(x_test) 
y_prediction
~~~



### 参考

[1] [Pandas Factorize](https://www.geeksforgeeks.org/python-pandas-factorize/)







![1635469534140](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1635469534140.png)