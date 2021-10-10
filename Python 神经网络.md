# Python 神经网络

### 1. 神经网络的定义

神经网络可以被简单的描述为将给定输入映射到所需输出的数学函数。

神经网络由以下组件组成：

* 一个输入层，$\hat{x}$ 
* 任意数量的隐藏层
* 一个输出层，$\hat{y}$
* 每层之间的一组权重和偏差，W 和 b
* 每个隐藏层的激活函数选择，$\sigma$。

下面是2层神经网络的结构（计算神经网络层数时，输入层往往被排除在外）：

![1633414570022](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633414570022.png)

再来，在 Python 中创建神经网络：

~~~python
class NeuralNetwork:
    def __init__(self, x ,y):
        self.input = x
        self.weights1 = np.random.rand(self.input.shape[1],4)
        self.weights2 = np.random.rand(4,1)
        self.y        = y
        self.output   = np.zeros(y.shape)
~~~



##### 1.1 训练神经网络

一个简单的 2 层神经网络的输出 $\hat{y}$ 是：

​									$\hat{y}=\sigma(W_2\sigma_(W_1x+b_1)+b_2)$ 		

只有权重 $W$ 和 偏差 $b$ 是影响输出 $\hat{y}$ 的变量。

权重和偏差的正确性决定了预测的强度。从输入数据中微调权重和偏差的过程称为**训练神经网络**。

训练过程的每次迭代包括以下步骤：

* 计算预测输出 $\hat{y}$ ，称为前馈
* 更新权重和偏差，称为反馈

以下是训练过程示意图：

![1633415977927](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633415977927.png)

前馈：

前馈是一个简单的微积分，对于基本的 2 层神经网络，神经网络的输出（前馈）是：

​										$\hat{y}=\sigma(W_2\sigma_(W_1x+b_1)+b_2)$ 		

~~~python
def feedforward(self):
    self.layer1 = sigmoid(np.dot(self.input,self.weights1))
    self.output = sigmoid(no.dot(self.layer1,self.weights2))
~~~

