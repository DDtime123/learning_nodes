# Python 机器学习（一）

### 1. 使用 Excel 进行回归分析

所采用的数据集是身高-体重数据集 (Weights_heights)。

因变量：体重

自变量：身高

(1) 20 条数据的回归分析：

回归方程式：y = 4.128x - 152.23

相关系数 R2：0.3254

![1633071562021](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633071562021.png)



![1633073074366](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633073074366.png)

​									回归直线图 1

(2) 200 条数据的回归分析：

回归方程式： y = 3.4317x - 105.96  

相关系数 R2：0.31

![1633072527392](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633072527392.png)



![1633073122323](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633073122323.png)

​										回归直线图 2

(3) 2000 条数据的回归分析：

回归方程式：  y = 2.9555x - 73.661  

相关系数 R2：0.2483

![1633072801849](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633072801849.png)



![1633073159178](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633073159178.png)

​											回归直线图 3





### 2. Python 使用sklearn库进行最小二乘法进行回归分析

#### 2.1 线性回归

以下是一组用于回归的方法，其中目标值预计是特征的线性组合，这里假设 y 是预测值：

![1633077062595](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633077062595.png)

在整个模块中，我们指定向量 w = (w1 , ... , w2) 作为系数和 w0 作为 截距。

##### 2.1.1 普通最小二乘法

线性回归拟合具有系数的线性模型 w = (w1 , ... , w2) 最小化数据集中观察到的目标与线性近似预测的目标之间的残差平方和。从数学上讲，它解决了以下形式的问题：

![1633077393701](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633077393701.png)

![1633077432496](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633077432496.png)

以下是使用 sklearn 的线性回归模型和数据集创建模型的过程，最后我们得到的是一组系数 w = (w1 , ... , w2) 。

线性回归模型将接受一个数据集并且存储模型的系数 w = (w1 , ... , w2)：

~~~python
from sklearn import linear_model
reg = linear_model.LinearRegression()
reg.fit([[0,0],[1,1],[2,2]],[0,1,2]) # 训练集和测试集
print(reg.coef_) # 打印出线性模型的系数 w = (w1 , ... , w2)
~~~



输出系数 (w1 , w2)：

![1633078029523](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633078029523.png)





#### 2.2 使用 pandas 库

##### 2.2.1 数据读取：

pandas 库的 read_excel 函数用于将 Excel 文件的内容作为 panda DataFrame 读入 python 环境。该函数可以通过使用文件的正确路径从操作系统读取文件。默认情况下，该函数将读入 Sheet1。

~~~python
import pandas as pd;
data = pd.read_excel('path/input.xls');
print(data);
~~~

该段代码将读取 input.xls 的 Sheet1 并将其打印出来。

read_excel 函数的常用用法有：

~~~python
pd.read_excel('tmp.xlsx',index_col=0) # 读取第一个表格的数据
pd.read_excel(open('tmp.xlsx','rb'),sheet_name='Sheet3') # 以只读方式打开文件，指定列表的名称为 'Sheet3'
...
~~~



这里使用只读方式打开，指定读取表格名称为 'weights_heights'。

~~~python
data = pd.read_excel(open('weights_heights.xls','rb'),sheet_name='weights_heights');
print(data)
~~~

![1633074963045](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633074963045.png)



然后将读取的数据切片。 read_excel 函数可以用来读取一些特定的行和列，这里我们可以使用称为 **.loc()** 的多轴索引方法，我们选择显示第 2 ，3 和 4 行和身高，体重两列：

~~~python
print(data.loc[[1,2,3],['Height','Weight']]);
~~~

![1633075324334](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633075324334.png)

我们也可以取得第 1 行到第 20 行，身高体重两列的数据：

~~~python
print(data.loc[1:20,['Height','Weight']])
~~~

通过如上方法我们可以获取指定行数的，指定字段的数据，现在是时候进行数据的分析了。



##### 2.2.2 数据分析：

首先引入 sklearn 库。

先来进行 20 行数据的回归分析，（注意 loc() 中指定行数的 0:19 跟列表 list 指定范围有所不同，列表指定 [start : end] 是指由索引 start 开始，到索引 end 的前一个为止，而 loc() 方法中 start : end 则是由索引 start 开始，到索引 end 为止）：

~~~python
# 20行数据
data_20 = data.loc[0:19,['Height','Weight']];
print(data_20)
~~~





以下进行对数据集构造回归曲线：

首先，从 sklearn 导入线性模型包，创建线性回归模型：

~~~python
# 导入线性模型包
from sklearn import linear_model;
# 创建线性回归模型
regr = linear_model.LinearRegression();
~~~

然后，往模型中加入数据集，fit 方法有两个参数，第一个是数据集的自变量，第二个是数据集的因变量，在这里自变量是身高，因变量是体重：

~~~python
# 加入训练集
regr.fit(data_20.loc[:,['Height']],data_20.loc[:,['Weight']]);
~~~

最后打印系数 w(w1 , ...  , w2) ：

~~~python
# 打印系数
print('系数: \n',regr.coef_);
~~~

因为只有一个自变量，所以只有一个系数：

![1633080369468](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633080369468.png)

然后获取相关系数 R2：

~~~python
# 获取相关系数 R2
print(regr.score(data_20.loc[:,['Height']],data_20.loc[:,['Weight']]));
~~~

![1633081058392](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633081058392.png)

最后获取截距：

~~~python
# 获取截距
print(regr.intercept_)
~~~

![1633081784064](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633081784064.png)

最后获得的回归方程是：

~~~xml
y = 4.128x - 152.234
R2 = 0.3254
~~~

然后我们需要使用数据可视化工具将回归曲线打印出来：

~~~python
# 引入 matplotlib 数据可视化库
import matplotlib.pyplot as plt;
# 打印数据集
plt.scatter(data_20.loc[:,['Height']],data_20.loc[:,['Weight']]);
# 打印回归曲线
a=regr.predict([[60],[90]]);
plt.plot([[60],[90]],a,'g-');
~~~

![1633087448148](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633087448148.png)



同理，可获取 200 行以及 2000 行数据的回归曲线：

~~~python
# 200 行数据的回归曲线
data_200 = data.loc[0:199,['Height','Weight']];
print(data_200,'\n')
# 加入训练集
regr.fit(data_200.loc[:,['Height']],data_200.loc[:,['Weight']]);
# 打印系数
print('系数: \n',regr.coef_);
# 获取相关系数 R2
print('R2：\n',regr.score(data_200.loc[:,['Height']],data_200.loc[:,['Weight']]));
# 获取截距
print('截距：\n',regr.intercept_)
~~~

![1633082671254](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633082671254.png)



200 行数据的回归方程是：

~~~xml
y = 3.432x - 105.9590
R2 = 0.31
~~~

![1633087593973](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633087593973.png)



~~~python
# 2000 行数据的回归曲线
data_2000 = data.loc[0:1999,['Height','Weight']];
print(data_2000,'\n')
# 加入训练集
regr.fit(data_2000.loc[:,['Height']],data_2000.loc[:,['Weight']]);
# 打印系数
print('系数: \n',regr.coef_);
# 获取相关系数 R2
print('R2：\n',regr.score(data_2000.loc[:,['Height']],data_2000.loc[:,['Weight']]));
# 获取截距
print('截距：\n',regr.intercept_)
~~~

![1633082683560](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633082683560.png)

2000 行数据的回归方程是：

~~~xml
y = 2.9555x - 73.661
R2 = 0.2483
~~~

![1633087606233](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633087606233.png)



#### 2.3 使用 python 实现最小二乘法





~~~python
from matplotlib import font_manager as fm, rcParams

plt.rcParams['font.sans-serif']=['SimHei'] #显示中文标签
plt.rcParams['axes.unicode_minus']=False   #这两行需要手动设置


##最小二乘法
import numpy as np   ##科学计算库 
import scipy as sp   ##在numpy基础上实现的部分算法库
import matplotlib.pyplot as plt  ##绘图库
from scipy.optimize import leastsq  ##引入最小二乘法算法


#设置样本数据，真实数据需要在这里处理

# print(data_2000.loc[:,['Height']].values.flatten())

##样本数据(Xi,Yi)，需要转换成数组(列表)形式
Xi=np.array(data_20.loc[:,['Height']].values.flatten())
Yi=np.array(data_20.loc[:,['Weight']].values.flatten())

'''
    设定拟合函数和偏差函数
    函数的形状确定过程：
    1.先画样本图像
    2.根据样本图像大致形状确定函数形式(直线、抛物线、正弦余弦等)
'''

##需要拟合的函数func :指定函数的形状
def func(p,x):
    k,b=p
    return k*x+b

##偏差函数：x,y都是列表:这里的x,y更上面的Xi,Yi中是一一对应的
def error(p,x,y):
    return func(p,x)-y

'''
    主要部分：附带部分说明
    1.leastsq函数的返回值tuple，第一个元素是求解结果，第二个是求解的代价值(个人理解)
    2.官网的原话（第二个值）：Value of the cost function at the solution
    3.实例：Para=>(array([ 0.61349535,  1.79409255]), 3)
    4.返回值元组中第一个值的数量跟需要求解的参数的数量一致
'''

#k,b的初始值，可以任意设定,经过几次试验，发现p0的值会影响cost的值：Para[1]
p0=[1,20]

#把error函数中除了p0以外的参数打包到args中(使用要求)
Para=leastsq(error,p0,args=(Xi,Yi))

#读取结果
k,b=Para[0]
print("k=",k,"b=",b)
print("cost："+str(Para[1]))
print("20行求解的拟合直线为:")
print("y="+str(round(k,2))+"x+"+str(round(b,2)))

# 

#画样本点
plt.figure(figsize=(8,6)) ##指定图像比例： 8：6
plt.scatter(Xi,Yi,color="green",label="样本数据",linewidth=2) 

#画拟合直线
x=np.linspace(60,90,100) ##在60-90直接画100个连续点
y=k*x+b ##函数式
plt.plot(x,y,color="red",label="拟合直线",linewidth=2) 
plt.legend(loc='lower right') #绘制图例
plt.show()
~~~

![1633095381906](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633095381906.png)

![1633095531677](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633095531677.png)

![1633095540621](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633095540621.png)

### 3. 总结

总体来说，无论是使用 Excel 还是使用 python 的 sklearn 库，又或是使用手动编写的最小二乘法来进行回归曲线拟合，得出的结果都无太大差别。

### 4. 参考

- [1] [线性回归 Python 实现](https://www.geeksforgeeks.org/linear-regression-python-implementation/)

- [2] [在 Python 中解决线性回归](https://www.geeksforgeeks.org/solving-linear-regression-in-python/?ref=rp)