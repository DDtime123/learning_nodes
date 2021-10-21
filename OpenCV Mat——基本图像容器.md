# OpenCV Mat——基本图像容器

数码相机，扫描仪，计算机断层扫描，核磁共振成像，我们通过这些方法从现实世界中获取数字图像，这些图像在我们人类眼中都一样，但是在数字设备中，它们看到的是图像中每个点的数值。



Mat 是一个包含两个数据部分的类：矩阵头（包括矩阵大小，用于存储的方法，存储矩阵的地址等信息）和指向包含矩阵的指针像素值（去任何维度取决于选择存储的方法）。矩阵头的大小是恒定的，但是矩阵本身的大小可能因图像而异，并且通常会大几个数量级。

~~~C++
Mat A, C;
A = imread(argv[1], IMREAD_COLOR);

Mat B(A); // 使用复制构造函数
C = A;	  // 赋值运算符
~~~

* OpenCV 函数的输出图像分配是自动的
* Mat 的赋值运算符和复制构造函数只复制标头
* 可以使用 cv::Mat::clone() 和 cv::Mat::copyTo() 函数复制图像的底层矩阵

~~~C++
Mat F = A.clone();
Mat G;
A.copyTo(G);
~~~



#### 1. 存储方法

关于存储像素值的方法，可以选择颜色空间和使用的数据类型。

* RGB
* HSV 和 HLS
* YCrCb
* CIE Lab



#### 2. 显示创建 Mat 对象

Mat 作为图像容器，同时也是一个矩阵类，可以创建和操作多维矩阵：

cv::Mat::Mat构造函数

~~~C++
Mat M(2,2,CV_8UC3,Scalar(0,0,255));
cout << "M= " << endl << M << endl << endl;
~~~

对于二维和多通道图像，首先定义它们的大小：行和列

然后需要指定用于存储元素的数据类型和每个矩阵点的通道数：

~~~C++
CV_[每项的位数][有符号或无符号][类型前缀]C[通道编号]
~~~



#### 3. 输出 Mat 对象

OpenCV 允许以格式化矩阵形式输出 Mat 对象：

~~~c++
 cout << "R (default) = " << endl << R << endl << endl;
~~~



#### 4. 参考

[1] [OpenCV Mat 对象](https://docs.opencv.org/3.2.0/d6/d6d/tutorial_mat_the_basic_image_container.html)