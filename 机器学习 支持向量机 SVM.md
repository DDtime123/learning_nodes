# 机器学习 支持向量机 SVM

### 1. SVM 简介

 **支持向量机 (SVM)**是一组用于分类、 回归和异常值检测的监督学习方法。

支持向量机的优点是：

* 在高维空间中有效。
* 在维数大于样本数的情况下仍然有效。
* 在决策函数中使用训练点的子集（称为支持向量），因此它也是内存高效的。
* 通用性：可以为决策函数指定不同的内核函数。提供了通用内核，但也可以指定自定义内核。

支持向量机的缺点包括：

* 如果特征数量远大于样本数量，在选择核函数时避免过度拟合，正则化项至关重要。

* SVM 不直接提供概率估计，这些是使用昂贵的五折交叉验证计算的

### 2. 使用 Sklearn 进行 SVM 

#### 2.1 数据准备

使用双月数据集，做两个交错的半圆。是一个简单的玩具数据集，用于可视化聚类和分类算法。

引入库：

~~~python
# inline plotting instead of popping out
%matplotlib inline

# python 3.6.8
import os, itertools, csv

from IPython.display import Image
from IPython.display import display

# numpy  1.19.5
import numpy as np

# pandas  0.25.3
import pandas as pd

# scikit-learn  0.22
from sklearn.compose import ColumnTransformer
from sklearn.datasets import make_moons
from sklearn.impute import SimpleImputer 
from sklearn.linear_model import LogisticRegression, Perceptron
from sklearn.metrics import accuracy_score
from sklearn.model_selection import GridSearchCV, train_test_split
from sklearn.neighbors import KNeighborsClassifier
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import LabelEncoder, OneHotEncoder, StandardScaler
from sklearn.svm import SVC

# matplotlib  3.1.2
import matplotlib
matplotlib.rcParams.update({'font.size': 22})
plt = matplotlib.pyplot

# load utility classes/functions that has been taught in previous labs
# e.g., plot_decision_regions()
from lib import *
~~~

生成数据集，双月数据集中的类是非线性可分的，可用二维表示：

~~~python
X, y = make_moons(n_samples=100, noise=0.15, random_state=0)
~~~

可视化数据集：

~~~python
plt.scatter(X[y == 0, 0], X[y == 0, 1],
            c='r', marker='o', label='Class 0')
plt.scatter(X[y == 1, 0], X[y == 1, 1],
            c='b', marker='s', label='Class 1')

plt.xlim(X[:, 0].min()-1, X[:, 0].max()+1)
plt.ylim(X[:, 1].min()-1, X[:, 1].max()+1)
plt.xlabel('$x_1$')
plt.ylabel('$x_2$')
plt.legend(loc='best')
plt.tight_layout()
plt.savefig('./fig-two-moon.png', dpi=300)
plt.show()
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105090048.png)



#### 2.2 使用线性分类器进行预测

如果应用线性分类器，比如感知器或逻辑回归，就不能得到合理的决策边界

~~~python
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=1)

sc = StandardScaler()
sc.fit(X_train)
X_train_std = sc.transform(X_train)
X_test_std = sc.transform(X_test)

X_combined_std = np.vstack((X_train_std, X_test_std))
y_combined = np.hstack((y_train, y_test))

ppn = Perceptron(max_iter=1000, eta0=0.1, random_state=0)
ppn.fit(X_train_std, y_train)
y_pred = ppn.predict(X_test_std)
print('[感知器回归]')
print('Misclassified samples: %d' % (y_test != y_pred).sum())
print('Accuracy: %.2f' % accuracy_score(y_test, y_pred))

# plot decision regions for Perceptron
plot_decision_regions(X_combined_std, y_combined,
                      clf=ppn, 
                      legend=range(y_train.size, 
                                     y_train.size + y_test.size))
plt.xlabel('$x_1$')
plt.ylabel('$x_2$')
plt.legend(loc='upper left')
plt.tight_layout()
plt.savefig('./fig-two-moon-perceptron-boundray.png', dpi=300)
plt.show()

lr = LogisticRegression(C = 1000.0, random_state = 0, solver = "liblinear")
lr.fit(X_train_std, y_train)

y_pred = lr.predict(X_test_std)
print('[逻辑回归]')
print('Misclassified samples: %d' % (y_test != y_pred).sum())
print('Accuracy: %.2f' % accuracy_score(y_test, y_pred))

# plot decision regions for LogisticRegression
plot_decision_regions(X_combined_std, y_combined,
                      clf=lr, 
                      legend=range(y_train.size, 
                                     y_train.size + y_test.size))
plt.xlabel('$x_1$')
plt.ylabel('$x_2$')
plt.legend(loc='upper left')
plt.tight_layout()
plt.savefig('.fig-two-moon-logistic-regression-boundray.png', dpi=300)
plt.show()
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105093928.png)

#### 2.3 使用 SVC 进行预测

~~~python
# kernel: 内核函数，可以是 'linear', 'poly', 'rbf', ...etc
# C 是错误惩罚项的超参数 
svm_linear = SVC(kernel='linear', C=1000.0, random_state=0)

svm_linear.fit(X_train_std, y_train)
y_pred = svm_linear.predict(X_test_std)
print('[线性 SVC]')
print('错误分类样本: %d' % (y_test != y_pred).sum())
print('准确度: %.2f' % accuracy_score(y_test, y_pred))

# 绘制线性 svm 的决策区域
plot_decision_regions(X_combined_std, y_combined,
                      clf=svm_linear, 
                      legend=range(y_train.size,
                                     y_train.size + y_test.size))
plt.xlabel('$x_1$')
plt.ylabel('$x_2$')
plt.legend(loc='upper left')
plt.tight_layout()
plt.savefig('./figtwo-moon-svm-linear-boundray.png', dpi=300)
plt.show()
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105100543.png)

使用线性的 SVC 达到了 85% 的准确率。

多项式内核：

~~~python
# C 是错误惩罚项的超参数 
# gamma 是 rbf 内核的超参数
svm_rbf = SVC(kernel='polynomial', random_state=0,  C=10.0)

svm_rbf.fit(X_train_std, y_train)
y_pred = svm_rbf.predict(X_test_std)
print('[多项式 SVC]')
print('错误分类样本: %d' % (y_test != y_pred).sum())
print('准确度: %.2f' % accuracy_score(y_test, y_pred))

# 绘制rbf svm 的决策区域
plot_decision_regions(X_combined_std, y_combined,
                      clf=svm_rbf, 
                      legend=range(y_train.size, 
                                     y_train.size + y_test.size))
plt.xlabel('$x_1$')
plt.ylabel('$x_2$')
plt.legend(loc='upper left')
plt.tight_layout()
plt.savefig('./fig-two-moon-svm-polynomial-boundray.png', dpi=300)
plt.show()
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105103834.png)

#### 2.4 使用非线性 SVC 进行预测

以上方法使用线性的内核函数，下面使用 rbf 内核的 SVC，RBF内核是一个使用高斯方法的内核。

rbf核函数用于将n维输入转换为m维输入，其中m远高于n，然后高效地找到更高维的点积。使用这个核的主要思想是：高维的线性分类器或回归曲线变成低维的非线性分类器或回归曲线。 

~~~python
# C 是错误惩罚项的超参数 
# gamma 是 rbf 内核的超参数
svm_rbf = SVC(kernel='rbf', random_state=0, gamma=0.2, C=10.0)

svm_rbf.fit(X_train_std, y_train)
y_pred = svm_rbf.predict(X_test_std)
print('[非线性 SVC]')
print('错误分类样本: %d' % (y_test != y_pred).sum())
print('准确度: %.2f' % accuracy_score(y_test, y_pred))

# 绘制rbf svm 的决策区域
plot_decision_regions(X_combined_std, y_combined,
                      clf=svm_rbf, 
                      legend=range(y_train.size, 
                                     y_train.size + y_test.size))
plt.xlabel('$x_1$')
plt.ylabel('$x_2$')
plt.legend(loc='upper left')
plt.tight_layout()
plt.savefig('./fig-two-moon-svm-rbf-boundray.png', dpi=300)
plt.show()
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105100612.png)

#### 2.5 调整超参数 C

虽然非线性 SVC 的准确率很高，但我们可以通过调整它的超参数已获得最佳性能，现在尝试其它值的超参数，调整超参数对于非线性 SVC 非常重要，不同的参数设置会产生巨大的性能差异。

调整超参数C：C在默认情况下是1，这是一个合理的默认选择。如果你有很多嘈杂的观察，你应该减少它，减少 C 对应于更多的正则化 。

~~~python
print('[非线性 SVC: C=1000, gamma=0.01]')
svm = SVC(kernel='rbf', random_state=0, gamma=0.01, C=1000.0)
svm.fit(X_train_std, y_train)
y_pred = svm.predict(X_test_std)
print('错误分类样本: %d' % (y_test != y_pred).sum())
print('准确度: %.2f' % accuracy_score(y_test, y_pred))

print('\n[非线性 SVC: C=1, gamma=1]')
svm = SVC(kernel='rbf', random_state=0, gamma=0.0001, C=10.0)
svm.fit(X_train_std, y_train)
y_pred = svm.predict(X_test_std)
print('错误分类样本: %d' % (y_test != y_pred).sum())
print('准确度: %.2f' % accuracy_score(y_test, y_pred))
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105102639.png)



### 3. 参考

[1] [KNN、SVM、数据预处理和 Scikit-learn 流水线](https://nthu-datalab.github.io/ml/labs/07_SVM_Pipeline/07_SVM_Pipeline.html)