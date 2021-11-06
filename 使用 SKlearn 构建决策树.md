# 使用 Sklearn 进行决策树算法

#### 1. 使用 Sklearn 构建决策树

使用决策树：

~~~python
%matplotlib inline

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from sklearn.preprocessing import LabelEncoder
from sklearn.feature_extraction import DictVectorizer

data=pd.read_csv('watermelons.csv')
data.head()
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105191442.png)

~~~python
# 展示类别
data['好瓜']
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105220540.png)



~~~python
# 展示所有的类别
data['好瓜'].unique()
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105220604.png)

~~~python
# 展示每一个类别的数量
class_group = data.groupby('好瓜').apply(lambda x: len(x))
class_group
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105220627.png)

~~~python
# 设置字体
plt.rcParams['font.sans-serif'] = ['KaiTi']
# 将类别按柱状图打印出来
class_group.plot(kind='bar', grid=False)
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105220656.png)



~~~python
from sklearn.model_selection import train_test_split
cols_to_retain = ['色泽','根蒂','敲声','纹理','脐部','触感']

X_feature = data[cols_to_retain]
X_dict = X_feature.T.to_dict().values()

# 将字典列表转化为 numpy 数组
vect = DictVectorizer(sparse=False)
X_vector = vect.fit_transform(X_dict)

# 打印所有属性
# vect.get_feature_names()

#le = LabelEncoder()
#y=le.fit_transform(data['好瓜'])
#X_train, X_test, y_train, y_test = train_test_split( X_vector, y, test_size=0.2, random_state=42)

# 0 to 14 is train set
X_Train = X_vector[:-1]
# 15th is test set
X_Test = X_vector[-1:] 

# Used to vectorize the class label
le = LabelEncoder()
y_train = le.fit_transform(data['好瓜'][:-1])
~~~

~~~python
from sklearn import tree

clf = tree.DecisionTreeClassifier(criterion='entropy')
clf = clf.fit(X_Train,y_train)
~~~

~~~python
# Predict the test data, not seen earlier
le.inverse_transform(clf.predict(X_Test))
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105220745.png)

~~~python
# prediction with the same training set
Train_predict = clf.predict(X_Train)
~~~

~~~python
# The model predicted the training set correctly
(Train_predict == y_train).all()
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105220813.png)

~~~python
# Metrics related to the DecisionTreeClassifier
from sklearn.metrics import accuracy_score, classification_report

print ('Accuracy is:', accuracy_score(y_train, Train_predict))
print (classification_report(y_train, Train_predict))
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105220834.png)

~~~python
import pydot
import pyparsing

from io import StringIO
dot_data = StringIO() 
tree.export_graphviz(clf, out_file=dot_data) 
graph = pydot.graph_from_dot_data(dot_data.getvalue()) 
graph[0].write_png('tree.png') 
#graph[0].create_png()
from IPython.core.display import Image 
Image(filename='tree.png')
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211105220905.png)

