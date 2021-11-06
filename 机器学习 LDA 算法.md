# 机器学习 LDA 算法

### 1. 线性判别分析 (LDA)

#### 1.1 LDA 简介

线性判别分析（Linear Discriminant Analysis, LDA）是一种线性分类，经常用来对数据进行降维。

该算法设计基于每个输入变量的特定观察分布为每个类别开发一个概率模型。然后通过计算它属于每个类的条件概率并选择具有最高概率的类来对新示例进行分类。

它是一个相对简单的概率分布模型，它对每一个输入变量的分布做出了强有力的假设，尽管违反了这些期望，它也可以做出有效的预测。

#### 1.2 学习 LDA 模型

LDA 对数据进行了一些简化的假设：

* 数据是高斯数据，绘制时每个变量的形状都想钟型曲线
* 每个属性都具有相同的方差，每个变量的值平均围绕均值变化相同的量

有了这些假设，LDA 模型会根据你的数据为每个类估计均值和方差。

每个类(k)的每个输入(x)的均值(mu)可以通过将值的总和除以值的总数的方式估计：

​													muk = 1/nk*sum(x)

其中muk是类k的x的平均值，nk是类k的实例数。方差在所有类别中计算为每个值与平均值的平均方差。

​									sigma^2 = 1/(nK)*sum((x-mu)^2)

其中 sigma^2 是所有输入(x)的方差，n是实例数，K是类别数，mu是输入x的均值。

#### 1.3 使用 LDA 进行预测

LDA通过一组新的具有每个类的概率的输入来进行预测。获取最高概率的类别作为输出类别，并且作出预测。

该模型使用贝叶斯定理来估计概率。贝叶斯定理可用于使用每个类别的概率和属于每个类别的数据的概率，在给定输入(x)的情况下估计输出类别(y)的概率：

​							P(Y=x|X=x)=(Plk*fk(x))/sum(Pll *f(x))

其中 Plk 是指在你的训练数据中观察到的每个类别(k)的基本概率（例如，对于在两个类别问题中50 50的拆分，则为0.5）。在贝叶斯定理中，这叫做先验概率：

​											Plk=nk/n

上面的 f(x) 是 x 属于该类的估计概率。高斯分布函数用于f(x)。将搞死分布函数带入上面的方程并简化，我们最终得到下面的方程，这称为判别函数，该类被计算为具有最大值的输出分类(y)：

Dk (x) = x * (muk/siga^2) - (muk^2/(2 * sigma^2)) + ln (PIk)

Dk(x) 是给定输入 x 的 k 类判别函数，muk、sigma^2 和 PIk 都是根据您的数据估计的。

#### 1.4  Sklearn 中的 LDA 分类预测建模



##### 1.4.1 Sklearn 中的 LDA 类

~~~python
class sklearn.discriminant_analysis.LinearDiscriminantAnalysis(solver='svd', shrinkage=None, priors=None, n_components=None, store_covariance=False, tol=0.0001, covariance_estimator=None)
~~~

线性判别分析，是具有线性决策边界的分类器，通过将类条件密度拟合到数据并使用贝叶斯规则生成。

该模型将高斯密度拟合到每个类，假设所有类共享相同的协方差矩阵。

拟合模型还可用于通过使用 transform 方法将输入投影到最具识别力的方向来降低输入的维数。

###### 1.4.1.1 LDA 类的参数

1)  slover(求解器): {'svd', 'lsqr', 'eigen'},default='svd' ，其中

* 'svd' ：奇异值分解(默认)。不计算协方差矩阵，因此此求解器被建议用于具有大量特征的数据。
* 'lsqr' :  最小二乘解。可以与收缩或自定义协方差估计其组合使用。
* 'eigen' : 特征值分解。可以与收缩或自定义协方差估计其组合使用。

2) shrinkage(收缩): 'auto' or float, default=None，其中

* None : 无收缩(默认)。
* 'auto' : 使用 ledoit-Wolf 引理自动收缩。
* 在 0 和 1 之间浮动 : 固定收缩参数。

3) priors(先验) : 是类似数组的数据 (n_classes, )，默认值是 None

类先验概率。默认情况下，类比例是从训练数据中推断出来的。

4) n_components : int, default=None

用于降维的组件数，如果没有，将设置为min(n_classes-1, n_features)。此参数仅影响 transform 方法。

5) store_covariance : bool, default=False

如果为 True，则在求解器为 'svd' 时显式计算加权的类内协方差矩阵。矩阵总是为其它求解器计算和存储。

6) tol : float, default=1.0e-4

X 的奇异值，被认为是显著的绝对阈值。用于估计 X 的秩。丢弃奇异值不显著的维度。仅在求解器为 'svd' 时使用。

7) convariance_estimator(协方差估计器) : default=None

如果不是 None，convariance_estimator 则用于估计协方差矩阵，而不是依赖经验协方差估计量 (具有潜在收缩)。该对象应该有一个 fit 方法和一个 cocariance_ 属性。

##### 1.4.2 使用 sklearn 进行线性判别分析

引入线性判别分析库：

~~~python
import numpy as np
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis
~~~

使用下列方法创建线性判别模型，该方法无需定义参数就可以直接使用，但可以根据需要配置自定义参数，比如 solver 求解器的选择和 惩罚的使用：

~~~python
...
# create the lda model
model = LinearDiscriminantAnalysis()
~~~

下面我们根据一个示例来演示线性判别分类方法。

首先，使用 make_classfication 函数创建一个包含1000个示例的合成分类数据集，每个示例有 10个输入变量。

~~~python
# 引入测试数据集
from sklearn.datasets import make_classification
# 定义数据集
X, y = make_classification(n_samples=1000, n_features=10, n_informative=10, n_redundant=0, random_state=1)
# 查看数据集行列
print(X.shape, y.shape)
~~~

下面可以通过 RepeatedStratifiedKFold 类使用 重复分层 K 折交叉验证来拟合和评估线性判别分析模型。我们在测试工具中使用 10 次折叠和 3 次重复。

下面是合成二元分类任务评估线性判别分析模型的完整示例：

~~~python
# 在数据集上评估 LDA 模型
from numpy import mean
from numpy import std
from sklearn.datasets import make_classification
from sklearn.model_selection import cross_val_score
from sklearn.model_selection import RepeatedStratifiedKFold
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis
# 定义数据集
X, y = make_classification(n_samples=1000, n_features=10, n_informative=10, n_redundant=0, random_state=1)
# 定义模型
model = LinearDiscriminantAnalysis()
# 定义模型的评估方法
cv = RepeatedStratifiedKFold(n_splits=10, n_repeats=3, random_state=1)
# 评估模型
scores = cross_val_score(model, X, y, scoring='accuracy', cv=cv, n_jobs=-1)
# 汇总结果
print('Mean Accuracy: %.3f (%.3f)' % (mean(scores), std(scores)))
~~~

运行该示例评估合成数据集上的线性判别分析算法，并报告10倍交叉验证的三个重复的准确度的平均值。

模型的平均准确度大约达到了 89.3% 的水准。

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211104214445.png)

我们可能使用线性判别分析作为我们的最终模型并对新数据进行预测。

这可以通过在所有可用数据上拟合模型并调用传入新数据行的 predict() 函数来实现。

以下是利用所有数据拟合模型并且对新数据行进行类标签预测的完整示例：

~~~python
# 在数据集上利用 lda 模型进行预测
from sklearn.datasets import make_classification
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis
# 定义数据集
X, y = make_classification(n_samples=1000, n_features=10, n_informative=10, n_redundant=0, random_state=1)
# 定义模型
model = LinearDiscriminantAnalysis()
# 拟合模型
model.fit(X, y)
# 定义新数据
row = [0.12777556,-3.64400522,-2.23268854,-1.82114386,1.75466361,0.1243966,1.03397657,2.35822076,1.01001752,0.56768485]
# 进行预测
yhat = model.predict([row])
# 对预测结果进行总结
print('Predicted Class: %d' % yhat)
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211104215311.png)

以上使用 LDA 算法对使用 make_classification 函数合成的数据进行了建模，并且使用建立的 LDA 模型对新数据进行分类预测。

### 2. 参考

[1] [使用 Python 进行线性判别分析](https://www.cnblogs.com/wj-1314/p/10234256.html)