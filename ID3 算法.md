# ID3 算法

#### 1. ID3算法步骤：

1.计算数据集的熵

2.对于每个属性/特征：      

​		1.计算所有分类值的熵 

​		2.取当前属性的平均信息熵 

​		3.计算当前属性的增益

3.选择增益最高的属性。

4.重复直到我们得到我们想要的树



#### 2. 分析 ID3 算法

这是一个 ID3 分类器的实现代码：

~~~python
class DecisionTreeClassifier:
    """使用 ID3 算法实现决策树."""

    # 决策树分类器初始化
    def __init__(self, X, feature_names, labels):
        self.X = X
        self.feature_names = feature_names
        self.labels = labels
        self.labelCategories = list(set(labels))
        self.labelCategoriesCount = [list(labels).count(x) for x in self.labelCategories]
        self.node = None
        
        self.entropy = self._get_entropy([x for x in range(len(self.labels))])  # calculates the initial entropy

    # 获取熵
    def _get_entropy(self, x_ids):
        """ 计算熵
        参数列表:
        __________
        :参数 x_ids: list, 包含实例ID的列表
        __________
        :返回值: entropy: float, 熵
        """
        # 根据实例ID给标签排序
        labels = [self.labels[i] for i in x_ids]
        # 计算每个类别的实例数
        label_count = [labels.count(x) for x in self.labelCategories]
        # 计算每个类别的熵并且将它们相加，并返回熵的和
        entropy = sum([-count / len(x_ids) * math.log(count / len(x_ids), 2) if count else 0 for count in label_count])
        return entropy

    # 获取信息增益
    def _get_information_gain(self, x_ids, feature_id):
        """根据给定特征的熵和系统的总熵计算特征的信息增益
        参数列表:
        __________
        :参数1 x_ids: list, 包含实例ID的列表
        :参数2 feature_id: int, 特征ID
        __________
        :返回值: info_gain: float, 给定特征的信息增益
        """
        # 计算总熵
        info_gain = self._get_entropy(x_ids)
        # 将所选特征的所有值存储在列表中
        x_features = [self.X[x][feature_id] for x in x_ids]
        # 获取唯一值
        feature_vals = list(set(x_features))
        # 获取每个值的频率
        feature_vals_count = [x_features.count(x) for x in feature_vals]
        # 获取特征值id
        feature_vals_id = [
            [x_ids[i]
            for i, x in enumerate(x_features)
            if x == y]
            for y in feature_vals
        ]

        # 使用所选特征计算信息增益
        info_gain = info_gain - sum([val_counts / len(x_ids) * self._get_entropy(val_ids)
                                     for val_counts, val_ids in zip(feature_vals_count, feature_vals_id)])

        # 返回信息增益
        return info_gain

    def _get_feature_max_information_gain(self, x_ids, feature_ids):
        """找到最大化信息增益的属性/特征。
        参数列表:
        __________
        :参数1 x_ids: list, 包含样本ID的列表
        :参数2 feature_ids: list, 包含特征ID的列表
        __________
        :返回值: string and int, 最大化信息增益的特征的特征和特征ID
        """
        # 获取每个特征的熵
        features_entropy = [self._get_information_gain(x_ids, feature_id) for feature_id in feature_ids]
        # 找到使信息增益最大化的特征
        max_id = feature_ids[features_entropy.index(max(features_entropy))]

        # 返回使信息增益最大化的特征和特征id
        return self.feature_names[max_id], max_id

    def id3(self):
        """初始化 ID3 算法以构建决策树分类器
        :return: None
        """
        x_ids = [x for x in range(len(self.X))]
        feature_ids = [x for x in range(len(self.feature_names))]
        self.node = self._id3_recv(x_ids, feature_ids, self.node)
        print('')

    def _id3_recv(self, x_ids, feature_ids, node):
        """ID3算法：它被递归调用，直到满足某些条件。
        参数列表:
        __________
        :参数1 x_ids: list, 包含样本id的列表
        :参数2 feature_ids: list, 包含特征id的列表
        :参数3 node: object, 类 Nodes 的一个实例
        __________
        :返回值: 包含决策树中节点所有信息的类 Node 的实例
        """
        if not node:
            node = Node()  # 初始化节点
        # 按实例 ID 排序标签
        labels_in_features = [self.labels[x] for x in x_ids]
        # 如果所有示例都具有相同的类（纯节点），则返回节点
        if len(set(labels_in_features)) == 1:
            node.value = self.labels[x_ids[0]]
            return node
        # 如果没有更多的特征要计算，则返回最可能的类节点
        if len(feature_ids) == 0:
            node.value = max(set(labels_in_features), key=labels_in_features.count)  # compute mode
            return node
        # else...
        # 选择使信息增益最大化的特征
        best_feature_name, best_feature_id = self._get_feature_max_information_gain(x_ids, feature_ids)
        node.value = best_feature_name
        node.childs = []
        # 每个实例所选特征的值
        feature_values = list(set([self.X[x][best_feature_id] for x in x_ids]))
        # 遍历所有值
        for value in feature_values:
            child = Node()
            child.value = value  # 从节点到我们特征中的每个特征值添加一个分支
            node.childs.append(child)  # 将新子节点追加到当前节点
            child_x_ids = [x for x in x_ids if self.X[x][best_feature_id] == value]
            if not child_x_ids:
                child.next = max(set(labels_in_features), key=labels_in_features.count)
                print('')
            else:
                if feature_ids and best_feature_id in feature_ids:
                    to_remove = feature_ids.index(best_feature_id)
                    feature_ids.pop(to_remove)
                # 递归调用算法
                child.next = self._id3_recv(child_x_ids, feature_ids, child.next)
        return node

    # 打印树
    def printTree(self):
        if not self.node:
            return
        nodes = deque()
        nodes.append(self.node)
        while len(nodes) > 0:
            node = nodes.popleft()
            print(node.value)
            if node.childs:
                for child in node.childs:
                    print('({})'.format(child.value))
                    nodes.append(child.next)
            elif node.next:
                print(node.next)
~~~

##### 2.1 选择使信息增益最大化的特征

~~~python
# 选择使信息增益最大化的特征
best_feature_name, best_feature_id = self._get_feature_max_information_gain(x_ids, feature_ids)
~~~

该函数调用 _get_feature_max_information_gain 方法以获取使信息增益最大化的特征，而 _get_feature_max_information_gain 方法通过循环调用 _get_information_gain 方法获取每个特征的熵获取使信息增益最大化的特征。每个特征的信息增益可以通过数据集的总熵减去该特征的熵获得。

~~~python
# 获取信息增益
def _get_information_gain(self, x_ids, feature_id):
    ...

def _get_feature_max_information_gain(self, x_ids, feature_ids):
    ...
    # 获取每个特征的熵
    features_entropy = [self._get_information_gain(x_ids, feature_id) for feature_id in feature_ids]
    ...
~~~

##### 2.2 以获得的使信息增益最大化的特征为节点，进行拆分

~~~python
node.value = best_feature_name
node.childs = []
# 每个实例所选特征的值
feature_values = list(set([self.X[x][best_feature_id] for x in x_ids]))
# 遍历所有值
for value in feature_values:
    child = Node()
    child.value = value  # 从节点到我们特征中的每个特征值添加一个分支
    node.childs.append(child)  # 将新子节点追加到当前节点
    child_x_ids = [x for x in x_ids if self.X[x][best_feature_id] == value]
    if not child_x_ids:
        child.next = max(set(labels_in_features), key=labels_in_features.count)
        print('')
    else:
        if feature_ids and best_feature_id in feature_ids:
            to_remove = feature_ids.index(best_feature_id)
            feature_ids.pop(to_remove)
~~~

##### 2.3 递归对子树使用同样的算法，直至节点的深度按照信息增益的高低从浅到深排序

~~~python
# 递归调用算法
                child.next = self._id3_recv(child_x_ids, feature_ids, child.next)
~~~

##### 2.4 递归条件

对子树重复执行算法，直到没有更多的特征需要计算，我们就得到树。

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211106090123.png)

~~~python
if not node:
        node = Node()  # 初始化节点
    # 按实例 ID 排序标签
    labels_in_features = [self.labels[x] for x in x_ids]
    # 如果所有示例都具有相同的类（纯节点），则返回节点
    if len(set(labels_in_features)) == 1:
        node.value = self.labels[x_ids[0]]
        return node
    # 如果没有更多的特征要计算，则返回最可能的类节点
    if len(feature_ids) == 0:
        node.value = max(set(labels_in_features), key=labels_in_features.count)  # compute mode
        return node
~~~



#### 3. 参考

[1] [西瓜书中ID3决策树的实现](https://blog.csdn.net/Leafage_M/article/details/79629074)