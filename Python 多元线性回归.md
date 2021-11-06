# Python 多元线性回归

本文接着上篇的[机器学习—数据清理](https://zhuanlan.zhihu.com/p/426974757)。

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

#### 1.2 处理分类变量

使用虚拟变量将分类变量为可处理的数据。使用 pandas 的 get_dummies 方法。

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
# 打印出线性模型的系数
print("系数(w0,w1...wn)：",LR.coef_)
# 获取截距
print("截距：",LR.intercept_)
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211101080416.png)

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211101080428.png)

#### 1.4 预测收益

~~~python
y_prediction = LR.predict(x_test) 
y_prediction
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211101080448.png)

#### 1.5 获取相关系数 R2

 现在，我们必须将 y_prediction 值与原始值进行比较，因为我们必须计算模型的准确度，这是由一个称为 R2 分数的概念实现的**。**让我们简要讨论 R2 分数： 

这是 sklearn.metrics 模块内部的一个函数，而 R2 的值在 0 和 1 之间，它跟 MSE 密切相关。

最好的 R2 分数是 1.0 ，它可以是负数。因为

~~~python
# 导入 r2_score 模块
from sklearn.metrics import r2_score
from sklearn.metrics import mean_squared_error
# 预测准确率
score = r2_score(y_test,y_prediction)
print("r2 : ",score)
print("均方误差 : ",mean_squared_error(y_test,y_prediction))
print("均方误差开方 : ",np.sqrt(mean_squared_error(y_test,y_prediction)))
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211101080842.png)

准确度仅有 0.54 。



#### 1.6 特征共线性的使用与否以及使用时机

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211101125527.png)

之前将方差膨胀因子 VIF 大于10的 area 从数据中删除掉，现在将 area 还回来，将bedrooms 和 bathrooms 去掉，重新求取 R2 和方差膨胀因子 VIF：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211101135704.png)

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211101135720.png)

精确度到达 0.67，各变量的 VIF 也小于 10 。

处理多重共线性的时机应该在整理出一个模型后，再做为优化模型的步骤进行。

#### 1.7 drop 掉某个特征与否与 drop 的时机

在之前我们在清理数据时将类别特征 neighborhood 去除掉了，现在将它还回来，并且整理为虚拟变量：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211102093644.png)

整理多重共线性：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211102094600.png)

最后查看精确度：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211102094647.png)

精确度达到 0.918。

#### 1.8 处理异常值

这里添加一个处理异常值的方法，在将因变量清除出数据集前使用它，这会让数据的可信度增加：

~~~python
# 异常值处理
# ================ 异常值检验函数：iqr & z分数 两种方法 =========================
def outlier_test(data, column, method=None, z=2):
    """ 以某列为依据，使用 上下截断点法 检测异常值(索引) """
    """
    full_data: 完整数据
    column: full_data 中的指定行，格式 'x' 带引号
    return 可选; outlier: 异常值数据框
    upper: 上截断点; lower: 下截断点
    method：检验异常值的方法（可选, 默认的 None 为上下截断点法），
    选 Z 方法时，Z 默认为 2
    """
    # ================== 上下截断点法检验异常值 ==============================
    if method == None:
        print(f'以 {column} 列为依据，使用 上下截断点法(iqr) 检测异常值...')
        print('=' * 70)
        # 四分位点；这里调用函数会存在异常
        column_iqr = np.quantile(data[column], 0.75) - np.quantile(data[column], 0.25)
        # 1，3 分位数
        (q1, q3) = np.quantile(data[column], 0.25), np.quantile(data[column], 0.75)
        # 计算上下截断点
        upper, lower = (q3 + 1.5 * column_iqr), (q1 - 1.5 * column_iqr)
        # 检测异常值
        outlier = data[(data[column] <= lower) | (data[column] >= upper)]
        print(f'第一分位数: {q1}, 第三分位数：{q3}, 四分位极差：{column_iqr}')
        print(f"上截断点：{upper}, 下截断点：{lower}")
        return outlier, upper, lower
    # ===================== Z 分数检验异常值 ==========================
    if method == 'z':
        """ 以某列为依据，传入数据与希望分段的 z 分数点，返回异常值索引与所在数据框 """
        """
        params
        data: 完整数据
        column: 指定的检测列
        z: Z分位数, 默认为2，根据 z分数-正态曲线表，可知取左右两端的 2%，
        根据您 z 分数的正负设置。也可以任意更改，知道任意顶端百分比的数据集合
        """
        print(f'以 {column} 列为依据，使用 Z 分数法，z 分位数取 {z} 来检测异常值...')
        print('=' * 70)
        # 计算两个 Z 分数的数值点
        mean, std = np.mean(data[column]), np.std(data[column])
        upper, lower = (mean + z * std), (mean - z * std)
        print(f"取 {z} 个 Z分数：大于 {upper} 或小于 {lower} 的即可被视为异常值。")
        print('=' * 70)
        # 检测异常值
        outlier = data[(data[column] <= lower) | (data[column] >= upper)]
        return outlier, upper, lower
~~~

~~~python
outlier, upper, lower = outlier_test(data=df, column='price', method='z')
outlier.info(); outlier.sample(5)
~~~

~~~python
# 这里简单的丢弃即可
df.drop(index=outlier.index, inplace=True)
~~~

做回归分析：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211102095725.png)



![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211102095545.png)

精确度为 0.916。

### 2. 参考

[1] [多元线性回归模型预测房价]

