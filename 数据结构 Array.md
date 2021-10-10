# 数据结构 Array

数组是连续位置的内存中存放的对象的集合。通过将多个相同项目存放在一起，可以简单地将偏移量添加到基值上，从而快速定位每个元素的位置。

![1633338819712](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633338819712.png)

#### 1. Array 旋转算法

##### 1.1 Array 旋转程序

编写一个函数 rotate(ar[], d, n) ，将大小为 n 的 arr[] 旋转 d 个元素

![1633339827906](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633339827906.png)

上述数组旋转 2 将使数组变为：

![1633339856974](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633339856974.png)

方法一：

在另一个 Array 之上旋转

移动 2 位：

arr[0...len-3] ----> arr2[2...len-1]

实现：

~~~python
arr2 = []
for i in range(arr.size()-2):
    arr2[i+2] = arr[i]
~~~

arr[len-2...len-1] ----> arr2[0...1]

实现：

~~~python
for i in range(2):
    arr2[arr.len-2+i] = arr[i]
~~~



方法二：

就在这个 Array 上旋转，使用临时数组

移动 2 位：

arr[0...len-3] ----> arr[2...len-1]

~~~python
temp = []
for i in range(arr.size()-3):
    temp.add(arr[arr.size()-3-i])
    arr[arr.size()-3-i] = arr[arr.size()-1-i]
~~~

arr[len-2...len-1] ----> arr[0...1]

~~~
for i in range(temp.size()):
    arr[i] = temp[i]
~~~