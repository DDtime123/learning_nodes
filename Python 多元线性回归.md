# Python 多元线性回归

### 1. 数据清理

数据科学家花费大量时间进行数据清理，以确保他们的数据是有效的。

数据清理的步骤主要包括：

* 删除不必要的列 DataFrame；
* 改变索引 DataFrame；
* 使用 .str() 方法清洁色谱柱；
* 使用 DataFrame.applymap() 函数来逐个元素地清理整个数据集；
* 将列重命名为一组更易于识别的标签；
* 跳过 CSV 文件中不必要的行。



#### 1.1 引入必要的库

这里使用 pandas 和 numpy 库进行数据清洗：

~~~python
import pandas as pd
import numpy as np
~~~

#### 1.2 在数据集中删除列

首先查看数据集的各个列：

~~~python
df = pd.read_csv("house_prices.csv")
df.head()
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211025201549.png)

并非所有数据文件中的数据类别都包含在数据集中，pandas 提供给我们简单的删除数据列的方法 DataFrame.drop() 。

其中 price 列是我们的因变量，将它剔除，neighborhood 没有意义，剔除：

~~~python
df.drop(['price','neighborhood'],inplace=True,axis=1)
df.head()
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211025203229.png)

在 drop 方法里，第一个参数定义了要删除的列的集合 [列1，列2...]，参数 inplace=True 是让方法直接在集合上进行修改，而非返回一个新集合，axis =1 则表明在列中寻找删除的对象。

也可以将 ['price'] 和 axis=1 修改为 columns=[列1，列2...] 来直接表明删除的就是列：

~~~python
df.drop(columns=['price'],inplace=True)
~~~



#### 1.3 更改一个索引

pandas 扩展了 numpy 库的数组索引，以实现更为通用的功能和标记。

使用 pandas 读入数据集时，会根据记录的顺序给它们安排索引，而有的时候我们想要使用每条记录的唯一值作为索引。

~~~python
df['house_id'].is_unique # 查看该列是否为唯一值
df.set_index('house_id',inplace=True) # pandas 重新设置索引
df.head()
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211025211510.png)

使用 df.loc[索引] 直接访问每一条记录：

~~~python
df.loc[1112]
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211025211526.png)

#### 1.4 整理数据中的字段

数据集中，有的字段使用文字来表示数据，比如说 地点Location。现在我们将清理这些列，并使它们采用统一格式。

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211026135124.png)

~~~python
df.dtypes.value_counts() # 查看 DataFrame 的列的数据类型
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211026131918.png)

这里有一个 Object 类型的列 style ，4 个 int64 类型的列。

这里我们想要将 style 列的值转化为唯一标识的 int id 值。使用 pandas.factorize() 方法。

pandas.factorize() 方法通过识别不同的值来帮助获取数组的不同表示。

~~~python
# sorting the numerics
label1, unique1 = pd.factorize(['b', 'd', 'd', 'c', 'a', 'c', 'a', 'b'], 
                                                           sort = True)
  
print("\n\nNumeric Representation : \n", label1)
print("Unique Values : \n", unique1)
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211026143155.png)

pandas.factorize() 方法的参数：

* [list]：一维数组
* sort：布尔值，默认是 False，排序的唯一性和随机标签
* na_sentinel：整数值，默认是 -1，当值是空的时的值。





### 2. 特征共线性

#### 2.1 共线性简介

多重共线性（也称共线性）是回归模型中的一个特征变量与另一个特征变量高度线性相关的现象。

如果存在两个或多个变量具有共线性，那么就意味着回归系数不是唯一确定的，它会损害模型的可解释性。因为一个变量的回归系数不是唯一的，且会受到其它变量的影响。

因为回归系数的解释是：当所有其他自变量保持不变时，当前自变量 $x_i$ 每变化一个单位时，因变量 $y$ 的平均变化。而我们希望的是当改变一个自变量的值时，不影响其它变量。

#### 2.2 共线性的类型

共线性具有两种基本类型：

* 结构多重共线性：当我们从数据本身而不是实际采样的数据创建新特征时会发生此种共线性。例如，当对一个变量进行平方或对某些变量应用一些算术来创建新变量时，新变量和原始变量之间将存在某种相关性。
* 数据多重共线性：这是我们看到的最常见的共线性。就是在抽样之后观察变量，可以看到部分变量之间在某种程度上相互关联。

#### 2.3 处理共线性的方法

##### 2.3.1 使用 VIF 检测共线性

在生态学中，当相关性大于 0.7 时，计算所有自变量之间的相关矩阵且删除其中一个是很常见的情况，这里可以选择计算每个变量的 “方差膨胀因子” VIF。

VIF 决定了自变量之间相关性的强度，它是通过取一个变量并将其它所有变量进行回归来预测的。

VIF 计算简单易懂：值越高，共线性越高。

为每个解释变量计算一个 VIF，并删除那些具有高值的变量。 “高”的定义有些随意，但通常使用 5-10 范围内的值。它采用以下公式： 

​												$VIF = \frac{1}{1-R^2}$

确定 $R^2$ 的值以找出 VIF ，$R^2$ 值越高，意味着该变量和其它变量相关性越强；$R^2$ 越接近 1 ，VIF 值越高。

~~~python
# Import library for VIF
from statsmodels.stats.outliers_influence import variance_inflation_factor

def calc_vif(X):

    # Calculating VIF
    vif = pd.DataFrame()
    vif["variables"] = X.columns
    vif["VIF"] = [variance_inflation_factor(X.values, i) for i in range(X.shape[1])]

    return(vif)
~~~

VIF 的特点如下：

* VIF 从 1 开始，没有上限
* VIF = 1，自变量与其他变量无相关性
* VIF 超过 5 或 10 表示该自变量和其它变量之间存在高度多重共线性。

~~~python
X = df.iloc[:,:]
calc_vif(X)
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211026144148.png)

此处 area，bedrooms，bathrooms和price都具有高度共线性，必须进行处理。

##### 2.3.2 开始修复共线性

修复共线性的方法之一是删除相关性高的一对特征中的一个，使用 pandas.DataFrame.drop 方法删除列。

首先删除 area 列，因为面积明显和房间数量（bedrooms，bathrooms）成相关关系，这个特征 VIF 值特别高：

~~~python
df.drop(columns=['area'],inplace=True)
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211026145927.png)

删除掉相关特征 area 后， 剩余特征的 VIF 值都下降了。

再删除相关性最高的 bedrooms 列，剩余特征的相关性就少了许多：

~~~python
df.drop(columns=['bedrooms'],inplace=True)
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211026150138.png)

但是，删除相关特征并不是唯一的解决方法，还有一种方法是合并相关特征，这里 bedrooms 的数量和 bathrooms 的数量其实可以加在一起，组合成个新的特征 rooms：

~~~python
df['rooms'] = df.apply(lambda x: x['bedrooms'] + x['bathrooms'], axis = 1)
X = df.drop(['bedrooms','bathrooms'],axis=1)
calc_vif(X)
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211026152546.png)





### 3. 多元线性回归

多元线性回归指具有两个或多个自变量的线性回归的情况。

如果只有两个自变量，估计的回归函数是 $f(x1,x2)=b_0+b_1x_1+b_2x_2$。它表示三维空间中的回归平面。回归的目标是确定权重 $b_0,b_1 和 b_2$ 的值，使得该平面尽可能接近实际响应并产生最小的 SSR（残差平方和）。



#### 3.1 回归模型假设

* 线性：因变量和自变量之间的关系应该是线性的
* 同方差性：应保持误差的恒定方差
* 多元正态性：多元回归假设残差呈正态分布
* 缺乏或没有多重共线性：假设数据中很少或没有多重共线性

虚拟变量：

这里我们使用了一个分类数据 style，它将非数值数据转换成了数值数据并添加到回归模型中。

自变量和因变量：

这里的因变量是 price，其余属性是自变量：

~~~python
df.drop(['neighborhood','price'],inplace=True,axis=1)
y = ['price'] # 将预测属性分离为 Y 进行模型训练
~~~







### 参考

[1] [Pandas Factorize](https://www.geeksforgeeks.org/python-pandas-factorize/)

