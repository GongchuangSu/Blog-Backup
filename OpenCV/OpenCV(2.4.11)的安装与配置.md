title: OpenCV(2.4.11)的安装与配置
layout: post
date: 2015-05-26 17:20:43  
comments: true
type: "categories"
categories: 
- OpenCV
tags: 
- OpenCV的安装与配置

---
# 下载软件
下载VS2010+OpenCV(2.4.11),并安装。

# 配置OpenCV
双击“计算机”，打开“系统属性”，点击“高级系统设置”，然后打开“环境变量”，编辑path,在变量值中添加以下语句：`D:\opencv\opencv\build\x86\vc10\bin;D:\opencv\opencv\build\x86\mingw\bin;D:\opencv\opencv\build\common\tbb\ia32\vc10` 
注意：`“D:\opencv”`具体应由软件OpenCV安装目录决定。若你安装在`D:\program\opencv`路径下，应将上面的`“D:\opencv”改为“D:\program\opencv”`。 

<!--more-->

![](http://i.imgur.com/xOobDpK.jpg)
![](http://i.imgur.com/EiN4UQK.jpg)
![](http://i.imgur.com/Oyy3y4g.jpg)

# 新建一个工程
1.打开文件->新建->项目->Visual C++->Win32->Win32控制台应用程序，输入名称“example”。 

![](http://i.imgur.com/hR0iabs.jpg)

2.点击确定，并进行下一步，在附加选项中勾选”空项目”，其他默认不变。点击完成。 

![](http://i.imgur.com/db0VNmE.jpg)

3.在源文件中添加新建项，新建一个C++文件。 

![](http://i.imgur.com/L0gp2lg.jpg)

![](http://i.imgur.com/gRJYv2L.jpg)

4.在VS2010中配置OpenCV

4.1打开项目下的属性（Alt+F7）,并打开属性页中配置属性下的VC++目录。 

![](http://i.imgur.com/0l2risi.jpg)

4.2配置包含目录 
编辑包含目录并添加以下语句: 
    D:\opencv\opencv\build\include 
    D:\opencv\opencv\build\include\opencv 
    D:\opencv\opencv\build\include\opencv2 

![](http://i.imgur.com/4SStnEZ.jpg)

4.3配置库目录 
编辑库目录并添加以下语句： 
    D:\opencv\opencv\build\x86\vc10\lib

![](http://i.imgur.com/wxIsBE9.jpg)

4.4配置链接器 
打开属性页中链接器下的输入，编辑附加依赖项并添加以下语句： 
    opencv_calib3d2411d.lib 
    opencv_contrib2411d.lib 
    opencv_core2411d.lib 
    opencv_features2d2411d.lib 
    opencv_flann2411d.lib 
    opencv_gpu2411d.lib 
    opencv_highgui2411d.lib 
    opencv_imgproc2411d.lib 
    opencv_legacy2411d.lib 
    opencv_ml2411d.lib 
    opencv_objdetect2411d.lib 
    opencv_ts2411d.lib 
    opencv_video2411d.lib 

![](http://i.imgur.com/eDlm46P.jpg)

特别注意：上面语句中2411是安装OpenCV的版本号，所以要根据具体安装的版本来填写。

4.5编程测试

下面就来写一个简单的图片显示的程序，在编程前将要显示的图片放在`D:\Documents\Visual Studio 2010\Projects\example\example`目录下，具体位置要根据你安装的位置决定。

```c++

//显示图像文件
#include <opencv2/opencv.hpp>
using namespace std;
#pragma comment(linker, "/subsystem:\"windows\" /entry:\"mainCRTStartup\"")
int main()
{
    const char *pstrImageName = "蓝天白云.jpg";
    const char *pstrWindowsTitle = "OpenCV第一个程序";
    //从文件中读取图像
    IplImage *pImage = cvLoadImage(pstrImageName, CV_LOAD_IMAGE_UNCHANGED);
    //创建窗口
    cvNamedWindow(pstrWindowsTitle, 0);
    //在指定窗口中显示图像
    cvShowImage(pstrWindowsTitle, pImage);
    //等待按键事件
    cvWaitKey();
    cvDestroyWindow(pstrWindowsTitle);
    cvReleaseImage(&pImage);
    return 0;
}

```

生成并运行可得： 

![](http://i.imgur.com/HJWWs3N.jpg)

# 参考资料
1. [【OpenCV入门指南】第一篇 安装OpenCV ](http://blog.csdn.net/morewindows/article/details/8225783 )
2. [VS2010+Opencv-2.4.0的配置攻略](http://www.cnblogs.com/freedomshe/archive/2012/04/25/2470540.html) 