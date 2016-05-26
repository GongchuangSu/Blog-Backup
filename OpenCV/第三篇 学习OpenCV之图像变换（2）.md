title: 第三篇 学习OpenCV之图像变换（2）
layout: post
date: 2015-06-28 10:15:15  
comments: true
type: "categories"
categories: 
- OpenCV
tags: 
- OpenCV
- 图像变换

---
上一节中，我们对图像进行了简单的高斯模糊处理，关键是创建图像并进行高斯处理。本节中，我们将对图像进行其他变换，实现对原图像进行宽、高减半处理，并在此基础上进行`canny`边缘检测处理，可通过控制滑动条对检测程度进行调节。 
# 简介

**Canny边缘检测**：Canny边缘检测算子是John F. Canny于 1986 年开发出来的一个多级边缘检测算法。Canny 的目标是找到一个最优的边缘检测算法，最优边缘检测的含义是： 
**好的检测**- 算法能够尽可能多地标识出图像中的实际边缘。 
**好的定位**- 标识出的边缘要尽可能与实际图像中的实际边缘尽可能接近。 
**最小响应**- 图像中的边缘只能标识一次，并且可能存在的图像噪声不应标识为边缘。

<!--more-->

**源代码：**

```C++
// 作者：GongchuangSu ( http://gongchuangsu.com/ )
// 时间：2015.06.28
// 说明：图像宽、高减半并进行canny边缘检测
#include "cv.h"
#include "highgui.h"
#pragma comment(linker, "/subsystem:\"windows\" /entry:\"mainCRTStartup\"")  //用于屏蔽控制台应用程序的窗口

int slider_position=0;
IplImage* src, * dst_canny, * dst_down;

//定义图像宽、高减半函数
IplImage* doPyrDown(IplImage* in )
{
    //确认输入图像的宽、高能够被2整除
    assert( in->width%2 == 0 && in->height%2 == 0 );
    IplImage* out = cvCreateImage(
        cvSize( in->width/2 , in->height/2),
        in->depth,
        in->nChannels
        );
    cvPyrDown( in , out );
    return( out );
}
//定义边缘检测函数
IplImage* doCanny(
    IplImage* in,
    double lowThresh,
    double highThresh,
    double aperture
    ){
        if( in->nChannels != 1)  //Canny只能处理灰度图片
            return(0);
        IplImage* out = cvCreateImage(
            cvGetSize( in ),
            in->depth,
            in->nChannels
            );
        cvCanny( in, out, lowThresh, highThresh, aperture );
        return( out );
};
//定义回调函数
void onTrackbarSlide(int threshhold){
    dst_canny = doCanny( dst_down, threshhold, threshhold*3, 3);
    cvNamedWindow( "边缘检测图", 1);
    cvShowImage( "边缘检测图", dst_canny);
}

int main()
{
    const char*ImageName="人物.jpg";
    src = cvLoadImage( ImageName, 0);  //注意读入图像的颜色应设置为灰色
    cvNamedWindow( "原图", 1);
    cvShowImage( "原图", src);
    //图像宽、高减半处理
    dst_down = doPyrDown( src );
    //图像边缘检测处理
    dst_canny = doCanny( dst_down, slider_position, slider_position*3, 3);
    cvNamedWindow( "边缘检测图", 1);
    cvShowImage( "边缘检测图", dst_canny);
    //创建滑动条
    cvCreateTrackbar(
        "滑动条",
        "原图",
        &slider_position,
        200,
        onTrackbarSlide
        );
    //等待按键触发
    cvWaitKey();
    cvReleaseImage(&src);
    cvReleaseImage(&dst_down);
    cvReleaseImage(&dst_canny);
    cvDestroyAllWindows();
    return 0;
}
```
# 相关函数
## cvPyrDown

**函数原型**：`void cvPyrDown(const CvArr* src, CvArr* dst, int filter=CV_GAUSSIAN_5x5 ) `
**函数说明**：使用高斯金字塔分解对输入图像向下采样。首先对输入图像用指定的滤波器进行卷积，然后通过拒绝偶数的行与列来向下采样图像。 
`src`：输入图像。 
`dst`：输出图像，其宽度和高度应是输入图像的一半，传入前必须完成初始化。 
`filter`:卷积滤波器的类型，目前仅支持`CV_GAUSSIAN_5x5`（默认） 。

## cvCanny

**函数原型**：`void cvCanny(const CvArr* image, CvArr* edges, double threshold1, double threshold2, int aperture_size=3 )` 
**函数说明**：采用Canny算法发现输入图像的边缘而且在输出图像中标识这些边缘。 
`image`：单通路-8位输入图像。 
`edges`：输出的边缘图像；跟输入图像有一样的大小和类型。 
`threshold1`：第一个阈值。 
`threshold2`：第二个阈值。 
`aperture_size`：Sobel算子内核大小。 
注：两个阈值当中的小阈值用来控制边缘连接，大的阈值用来控制强边缘的初始分割。

## 运行结果
**原图：**
![](http://i.imgur.com/ETLCFRx.jpg)

**边缘检测图：**
![](http://i.imgur.com/M0BNlGN.jpg)