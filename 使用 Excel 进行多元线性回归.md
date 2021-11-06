# 使用 Excel 进行多元线性回归

### 1. 数据清理

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211102125924.png)



将 house_id 删除掉，它没有统计的意义。

数据集中有类别型数据 neighborhood 和 style，要将它们转化为虚拟变量。

使用 Excel 中的 IF 函数，选择空白的一列，并在上设置函数：

就能将 neighborhood 列的 A 类别转化为一列。

如此类推，将所有类别数据转化为虚拟变量，同时将原来的列删除掉：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211102144001.png)



### 2. 多元线性回归

使用 Excel 数据分析工具进行回归分析，将 price 列设置为因变量，其他列设置为自变量：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211102134010.png)

得到回归结果：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211102144138.png)

接下来继续处理数据。

F检验是整体回归方程的显著性检验。如果不显著，可能是这个变量和被解释变量没有相关关系，又或者是由于解释变量过多，由多重共线性引起。

P 值是用于t检验和f检验的衡量指标。

一般以 P < 0.05 为显著，P < 0.01 为非常显著，其含义是样本间的差异由抽样误差所致的概率小于 0.05 或 0.01。 如果大于0.05，我们有理由相信该变量前的系数是0，直接删掉该特征。 



