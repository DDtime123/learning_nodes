### OpenCV图像编程入门（一）

#### 1. OpenCV概述

OpenCV是一个开源的程序库，用于开发在Windows，Linux，Android和Mac OS中运行的计算机视觉应用程序。在 BSD 许可协议下，它可以用来开发学术应用和商业应用，可随意使用，发布和修改。

##### 1.1 OpenCV安装

Windows平台：到官网下载 Windows 版安装包，安装即可：[openCV 下载](https://opencv.org/releases/)：

![1632917594672](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632917594672.png)



安装好后，OpenCV安装目录如下：

![1632917882571](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632917882571.png)



Ubuntu 16.4 平台安装

参考：[C++ opencv 3.3 install on ubuntu 16.4](https://www.cnblogs.com/cxxszz/p/7338909.html)

##### 1.2 Visual Studio 2019 配置 OpenCV 开发环境

(1) 首先，配置系统环境变量

在系统环境变量的 Path 中添加 opencv\build\x64\vc14\bin 。根据不同版本的OpenCV， “vc14” 这个文件夹名称可能不同，也可能有多个 “vc15”，“vc13”之类的文件夹，但是基本目录结构相同：

![1632917968362](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632917968362.png)



(2) 接下来在 Visual Studio 中配置 OpenCV 的库

首先新建一个 C++ 的空项目：

![1632918654235](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632918654235.png)



打开：视图 -> 其它窗口 -> 属性管理器：

![1632918828363](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632918828363.png)



接下来会多出一个 **属性管理器** 工作区，我们选择在这里配置项目的属性。这是因为在这里配置项目的属性，相当于给 IDE 的全局配置了属性，以后再创建 OpenCV 的工程项目就不用再次配置项目属性了：

![1632919344791](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632919344791.png)

右击 ”Debug | 64“ ，点击属性，我们就可以配置 IDE 的全局属性了：

![1632919762252](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632919762252.png)



首先配置头文件（h，hpp）。在 **通用属性 -> VC++目录 -> 包含目录** 中添加以下三个目录：

![1632920019103](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632920019103.png)

三个目录：

~~~xml
F:\OPENCV\opencv\build\include\opencv2
F:\OPENCV\opencv\build\include\opencv
F:\OPENCV\opencv\build\include
~~~



然后配置工程库（lib）目录。在 **通用属性 -> VC++目录 -> 库目录** 中添加目录：

~~~xml
F:\OPENCV\opencv\build\x64\vc14\lib
~~~



最后配置链接库（DLL）。在 **通用属性 -> 链接器 -> 输入 -> 附加依赖项** 中添加两个 DLL 动态库文件：

~~~xml
opencv_world310d.lib
opencv_world310.lib
~~~

这两个文件根据自己实际的 OpenCV 版本来确定，不同版本的 OpenCV 库这两个文件的名称不同，具体到自己安装 OpenCV 的目录中的该目录查找：

~~~xml
opencv\build\x64\vc14\lib
~~~



如果只是打开：项目 -> 属性，我们只能配置当前项目的属性：

![1632919456920](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632919456920.png)

![1632919507060](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632919507060.png)



最后我们把两个动态库文件复制到  C:\Windows\SysWOW64 

~~~xml
opencv_world310d.lib
opencv_world310.lib
~~~



以上就是 Visual Studio 2019 配置 OpenCV 库的过程，接下来通过一个使用 OpenCV 库调用摄像头的程序验证自己的开发环境吧：

~~~c++
#include <opencv2/opencv.hpp>



using namespace cv;



int main()

{

	VideoCapture capture(0);

	Mat edges;

	namedWindow("调用摄像头");

	while (1)

	{

		Mat frame;

		capture >> frame;

		cvtColor(frame, edges, CV_BGR2GRAY);

		blur(edges, edges, Size(7, 7));

		Canny(edges, edges, 0, 30, 3);

		imshow("边缘检测摄像头", edges);

		if (waitKey(30) >= 0) break;

	}

	return 0;

}
~~~

注意我们配置的 Debug | 64 项目的全局属性，要切换到这个调试配置，否则 IDE 无法找到 OpenCV 库的头文件，也无法编译成功：

![1632920893138](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632920893138.png)

###### 1.2.1 题目与代码分析

(1) 修改OpenCV VideoCapture类的视频源，使其从本地视频中读入数据。

VideoCapture capture：Videocapture 类是从视频文件，图像序列或相机捕获视频的类。该类提供 C++ API，用于从相机捕获视频或读取视频文件和图像序列。

VideoCapture(参数...) 中的 (参数...) 可以是“文档名称”，“索引”，“指数”，“文件名，apiPreference”，“索引，apiPreference”。

~~~C++
VideoCapture capture(0);
~~~

0 为 相机的设备ID。

如果要换成从视频文件读入数据，仅需将VideoCapture的构造方法改成：

~~~c++
VideoCapture capture("path/file.mp4");
~~~



(2) 分析 waitKey 函数的作用，以及使用它增加主动退出程序的途径。

waitKey()：OpenCV 中的 cv::waitKey(n) 函数用于在将图像渲染到窗口时引起毫秒延迟，当使用时，它返回用户在活动窗口中按下的键（ 的ASCII值 ） ，所以，想要让程序在接受到按键时停止摄像程序，只需要根据 waitKey() 的返回值判断退出程序主循环即可：

~~~C++
while(1){
    ...
    
    if(waitKey(30) > 0) break;
    ...
}
~~~



(2) 使用 OpenCV 读取图像并且提高图像质量。

首先读取图像：

~~~C++
#include <opencv2/opencv.hpp>
#include <iostream>
#include <string.h>

using namespace cv;



int main()
{
    // 读取图像文件
	Mat image = imread("flower.jpg");
	// 检查错误
	if (image.empty()) 
	{
		std::cout << "Can not open Image" << std::endl;
		std::cin.get();
		return -1;
	}
    
	std::string windowName = "图像处理"; // 图像的名称

	namedWindow(windowName); // 创建窗口

	imshow(windowName, image); // 在窗口处加载图像

	waitKey(0); // 等待键盘事件

	destroyWindow(windowName); // 销毁窗口

	return 0;

}
~~~

改变图像对比度和亮度：

~~~C++
#include <opencv2/opencv.hpp>
#include <iostream>
#include <string.h>

using namespace cv;



int main()
{
    // 读取图像文件
	Mat image = imread("flower.jpg");
	// 检查错误
	if (image.empty()) 
	{
		std::cout << "Can not open Image" << std::endl;
		std::cin.get();
		return -1;
	}
    
	// 对 Mat 矩阵数据类型进行操作

	Mat imageContrastHigh2;
	image.convertTo(imageContrastHigh2, -1, 2, 0);

	Mat imageContrastHigh4;
	image.convertTo(imageContrastHigh4, -1, 4, 0);

	Mat imageContrastLow0_5;
	image.convertTo(imageContrastLow0_5, -1, 0.5, 0);

	Mat imageContrastLow0_25;
	image.convertTo(imageContrastLow0_25, -1, 0.25, 0);


	// 图像名称
	std::string windowName = "原图像";
	std::string windowNameContrastHigh2 = "像素对比度提高 2";
	std::string windowNameContrastHigh4 = "像素对比度提高 4";
	std::string windowNameContrastLow0_5 = "像素对比度降低 0.5";
	std::string windowNameContrastLow0_25 = "像素对比度降低 0.25";

	namedWindow(windowName); // 创建窗口
	namedWindow(windowNameContrastHigh2);
	namedWindow(windowNameContrastHigh4);
	namedWindow(windowNameContrastLow0_5);
	namedWindow(windowNameContrastLow0_25);

	imshow(windowName, image); // 在窗口处加载图像
	imshow(windowNameContrastHigh2, image);
	imshow(windowNameContrastHigh4, image);
	imshow(windowNameContrastLow0_5, image);
	imshow(windowNameContrastLow0_25, image);

	waitKey(0); // 等待键盘事件

	destroyWindow(windowName); // 销毁窗口
	destroyWindow(windowNameContrastHigh2);
	destroyWindow(windowNameContrastHigh4);
	destroyWindow(windowNameContrastLow0_5);
	destroyWindow(windowNameContrastLow0_25);

	return 0;

}
~~~

处理过后图像的质量如下：

![1633612416705](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633612416705.png)



##### 1.3 Ubuntu 平台下配置 OpenCV 开发环境

~~~ Makefile
CC = g++
PROJECT = project_name
SRC = main.cpp
LIBS = `pkg-config --cflag --libs opencv`

$(PROJECT) : $(SRC)
	$(CC) $(SRC) -o $(PROJECT) $(LIBS)
~~~

OpenCV Makefile 配置如上。



#### 2. 常用OpenCV 库

OpenCV 库根据功能不同分成了好几个模块，这些模块都是内置的库文件，位于 lib 目录下，常用的模块有：

* core 模块：包含了程序库的核心功能，包括基本的数据结构和算法；
* imgporc 模块：包含了主要的图像处理函数；
* highgui 模块：包含图像，视频读写函数和部分用户界面函数；
* features2d 模块：包含特征点检测器，描述子以及特征点匹配框架；
* calib3d 模块：包含相机标定，双视角几何估计以及立体函数；
* video 模块：包含运动估计，特征跟踪以及前景提取函数和类；
* objdetect 模块：包括目标检测函数，例如面部和人体探测器；

除了以上常用的模块以外，OpenCV 库还包含了其他实用的模块：机器学习模块（ml），计算几何算法（flann），共享代码（contrib），过时的代码（legacy）以及GPU加速代码（gpu）等。

如果使用到某些 OpenCV 函数，就必须在链接时将程序与包含这些函数的库相连。每个模块都有一个对应的头文件（位于 include 目录）。包含头文件的格式如下：

~~~c++
#include <opencv2/core/core.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/highgui/highgui.hpp>
~~~



#### 3. 使用 OpenCV 库装载，显示和存储图像

首先要确定实现功能所需的头文件，这里我们要简单的显示一个图像，所以需要定义了图像数据结构的核心库（core）以及包含了所有图形接口函数的 highgui 头文件：

~~~c++
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
~~~

在 main 函数中，首先定义一个表示图像的变量。在 OpenCV2 中，定义 cv::Mat 的对象：

~~~c++
cv::Mat image; // 创建一个新图像
~~~

这个定义创建了一个尺寸为 0x0 的图像，可以通过方为 cv::Mat 的 size 属性来验证：

~~~c++
std::cout << "This image is " << image.rows << " x " << image.cols << " y " << std::endl;
~~~

接下来调用 cv::Mat 的读函数，就可以读入一个图像，解码，然后分配内存：

~~~c++
image = cv::imread("股票.svg"); // 读取输入图像
~~~



#### 4. Mat数据结构



#### 6. OpenCV 人脸识别

~~~c++
#include <opencv2/objdetect.hpp>
#include <opencv2/highgui.hpp>
#include <opencv2/imgproc.hpp>
#include <iostream>

using namespace std;
using namespace cv;

// 检测人脸的函数
void detectAndDraw(Mat& img, CascadeClassifier& cascade,
	CascadeClassifier& nestedCascade, double scale);

string cascadeName, nestedCascadeName;

int main(int argc, const char** arg) {

	// Video 捕获类， 用于为检测到的人脸播放视频 
	VideoCapture capture;
	Mat frame, image;

	// 预训练好的， 具有面部特征的 XML 分类器 
	CascadeClassifier cascade, nestedCascade;
	double scale = 1;

	// 从 "opencv/data/haarcascades" 目录加载分类器 
	nestedCascade.load("F:/OPENCV/opencv/sources/data/haarcascades/haarcascade_eye.xml");

	// 在程序执行前改变目录
	cascade.load("F:/OPENCV/opencv/sources/data/haarcascades/haarcascade_frontalcatface.xml");

	// Start Video..1) 0 for WebCam 2) "Path to Video" for a Local Video
	capture.open(0);
	if (capture.isOpened()) {

		// 从 video 里获取画面（帧）， 还有监测人脸
		cout << "Face Detection Started...." << endl;
		while (1) {
			capture >> frame;
			if (frame.empty())
				break;
			Mat frame1 = frame.clone();
			detectAndDraw(frame1, cascade, nestedCascade, scale);
			char c = (char)waitKey(10);

			// Press q to exit from window
			if (c == 27 || c == 'q' || c == 'Q')
				break;
		}
	}
	else
		cout << "Could not Open Camera";

	return 0;
}

void detectAndDraw(Mat& img, CascadeClassifier& cascade,
	CascadeClassifier& nestedCascade,
	double scale)
{
	vector<Rect> faces, faces2;
	Mat gray, smallImg;

	cvtColor(img, gray, COLOR_RGB2GRAY); // 转换到 灰色的范围
	double fx = 1 / scale;

	// 重新设定 灰色图像的大小
	resize(gray, smallImg, Size(), fx, fx, INTER_LINEAR);
	equalizeHist(smallImg, smallImg);

	// 使用级联分类器 检测不同尺寸的脸 
	cascade.detectMultiScale(smallImg, faces, 1.1,
		2, 0 | CASCADE_SCALE_IMAGE, Size(30, 30));

	// 画一个圆形包围脸
	for (size_t i = 0; i < faces.size(); i++) {
		Rect r = faces[i];
		Mat smallImgROI;
		vector<Rect> nestedObjects;
		Point center;
		Scalar color = Scalar(255, 0, 0); // 给绘画工具设置颜色
		int radius;

		double aspect_ratio = (double)r.width / r.height;
		if (0.75 < aspect_ratio && aspect_ratio < 1.3)
		{
			center.x = cvRound((r.x + r.width * 0.5) * scale);
			center.y = cvRound((r.y + r.height * 0.5) * scale);
			radius = cvRound((r.width + r.height) * 0.25 * scale);
			circle(img, center, radius, color, 3, 8, 0);
		}
		else
			rectangle(img, cvPoint(cvRound(r.x * scale), cvRound(r.y * scale)),
				cvPoint(cvRound((r.x + r.width - 1) * scale),
					cvRound((r.y + r.height - 1) * scale)), color, 3, 8, 0);
		if (nestedCascade.empty())
			continue;
		smallImgROI = smallImg(r);

		// 从输入图像中，检测眼睛
		nestedCascade.detectMultiScale(smallImgROI, nestedObjects, 1.1, 2,
			0 | CASCADE_SCALE_IMAGE, Size(30, 30));
		// 在眼睛上画一个圆形
		for (size_t j = 0; j < nestedObjects.size(); j++) {
			Rect nr = nestedObjects[j];
			center.x = cvRound((r.x + nr.x + nr.width * 0.5) * scale);
			center.y = cvRound((r.y + nr.y + nr.height * 0.5) * scale);
			radius = cvRound((nr.width + nr.height) * 0.25 * scale);
			circle(img, center, radius, color, 3, 8, 0);
		}

		// 展现出处理过后 检测到的脸 的图像 
		imshow("Face Detection", img);
	}
}
~~~







#### 7. 遇到问题

问题：在 Ubuntu 虚拟机中使用摄像头无法获取图像，运行显示： select timeout。

解决方法：关闭虚拟机，在虚拟机设置里将USB控制器设置为USB3.0兼容模式。

![1633595634491](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633595634491.png)