title: 第三篇 学习OpenCV之图像变换（1）
layout: post
date: 2015-06-22 17:27:43  
comments: true
type: "categories"
categories: 
- OpenCV
tags: 
- OpenCV
- 图像变换

---
图像可以进行多种方式的变换，其中一种简单的变换就是对图像进行平滑处理，通过对图像数据与高斯或者其他核函数进行卷积，有效的减少图像信息内容。 
**高斯模糊**，也叫高斯平滑，根据高斯曲线调节象素色值，有选择地模糊图像，通常用它来减少图像噪声以及降低细节层次。 
接下来，将编程实现载入一幅图像并进行平滑处理。

<!--more-->

```C++
// 作者：GongchuangSu (http://gongchuangsu.com/)
// 时间：2015.06.22
// 说明：载入图像并利用高斯模糊进行平滑处理

#include "cv.h"
#include "highgui.h"
#pragma comment(linker, "/subsystem:\"windows\" /entry:\"mainCRTStartup\"")

//图像平滑处理函数
void doSmooth(IplImage* in)
{
    cvNamedWindow("Image_dst");
    //为处理后的图像分配存储空间
    IplImage* dst = cvCreateImage(
        cvGetSize(in),
        IPL_DEPTH_8U,
        3
    );
    //平滑处理
    cvSmooth( in, dst, CV_GAUSSIAN, 5, 5 );
    cvShowImage("Image_dst",dst);
    cvReleaseImage( &dst );
}

int main()
{
    const char*ImageName="人物.jpg";
    IplImage* src = cvLoadImage( ImageName );
    cvNamedWindow("Image_src");
    cvShowImage("Image_src",src);
    //调用处理函数
    doSmooth( src );
    cvWaitKey(0);
    cvReleaseImage( &src );
    cvDestroyAllWindows();
}
```
# cvCreateImage

**函数原型**：`IplImage* cvCreateImage(CvSize size, int depth, int channels)` 
**函数说明**：创建图像头并分配数据。 
`size`：图像的宽度和高度。可直接用函数`CvGetSize`取得某个图像的大小并返回，也可用函数`cvSize( int width, int height )`赋值，具体如下。该函数`cvSize`内部已定义，可直接调用。

```c++
CvSize  cvSize( int width, int height )
{
    CvSize s;

    s.width = width;
    s.height = height;

    return s;
}
```

`depth`：图像元素的位深度，可以是下面的其中之一： 
`IPL_DEPTH_8U` - 无符号8位整型 
`IPL_DEPTH_8S` - 有符号8位整型 
`IPL_DEPTH_16U` - 无符号16位整型 
`IPL_DEPTH_16S` - 有符号16位整型 
`IPL_DEPTH_32S` - 有符号32位整型 
`IPL_DEPTH_32F` - 单精度浮点数 
`IPL_DEPTH_64F` - 双精度浮点数 
`channels`：每个像素的颜色通道数。可选择1,2,3或4。该函数只能创建通道是交叉存取的图像，例如通常的彩色图像数据排列是：`b0 g0 r0 b1 g1 r1···`

# CvSize

结构体`CvSize`表示一个矩阵或一个图像的大小。

```c++
typedef strcut CvSize
{
  int width;   //宽度
  int height;  //高度
}size;
```
# CvGetSize

**函数原型**：`CvSize cvGetSize(const CvArr* arr) `
**函数说明**：返回矩阵或图像ROI的大小（ROI：Region Of Interest 感兴趣区）。函数返回矩阵或图像的行数（`CvSize::height`）和列数（`CvSize::width`），如果是图像就返回ROI的大小。

# CvArr

不确定数组 `typedef void CvArr`; 
`CvArr*` 仅仅是被用于作函数的参数，用于指示函数接收的数组类型可以不止一个，如 `IplImage*`, `CvMat*` 甚至 `CvSeq*`. 最终的数组类型是在运行时通过分析数组头的前4 个字节判断。

# cvSmooth

**函数原型**：`void cvSmooth(const CvArr* src, CvArr* dst, int smoothtype=CV_GAUSSIAN, int param1=3, int param2=0, double param3=0, double param4=0 )` 
**函数说明**：图像平滑处理的各种方法。 
`src`：输入图像。 
`dst`：输出图像。 
`smoothtype`：平滑方法。 
`CV_BLUR_NO_SCALE` (简单不带尺度变换的模糊) - 对每个象素的`param1×param2` 领域求和。如果邻域大小是变化的，可以事先利用函数 `cvIntegral` 计算积分图像； 
`CV_BLUR (simple blur)` - 对每个象素`param1×param2`邻域求和并做尺度变换` 1/(param1?param2)`； 
`CV_GAUSSIAN (gaussian blur)` - 对图像进行核大小为 `param1×param2` 的高斯卷积 ； 
`CV_MEDIAN (median blur)` - 对图像进行核大小为`param1×param1` 的中值滤波； 
`CV_BILATERAL` (双向滤波) - 应用双向 3x3 滤波，彩色 `sigma=param1`，空间 `sigma=param2`。 
`param1`：平滑操作的第一个参数，孔径的宽度。一定是个正奇数（1,3,5···） 
`param2`：平滑操作的第二个参数，孔径的高度。对于`CV_MEDIAN` 和`CV_BILATERAL` ，该参数被忽略；对于简单/非尺度变换的高斯模糊的情况，如果`param2`的值为零，则表示其被设定为`param1`。其他情况下，它一定是个正奇数。 
`param3`：对应高斯参数的 `Gaussian sigma` (标准差)。具体参考手册。 
注：简单模糊和高斯模糊支持1-或3-通道，8-比特和32-比特浮点图像。中值和双向滤波工作于1-或3-通道，8-位图像。

# 运行结果

**处理前：**
![](http://i.imgur.com/WDV7C2w.jpg)

**处理后：**
![](http://i.imgur.com/I80bVXx.jpg)
