title: 第一篇 学习OpenCV之图像显示
layout: post
date: 2015-06-14 22:03:43  
comments: true
type: "categories"
categories: 
- OpenCV
tags: 
- OpenCV
- 图像显示

---

OpenCV开发包提供了读取各类型的图像文件、视频内容以及摄像机输入的功能，这些功能都包含在OpenCV所包含的HighGUI库中。下面，利用其中的一些功能编写一段简单的用于读取并在屏幕上显示一张图像的程序。 

例：从指定文件夹中读取图片并显示在屏幕上

<!--more-->

```C++
#include "highgui.h"
#pragma comment(linker, "/subsystem:\"windows\" /entry:\"mainCRTStartup\"") //用于屏蔽控制台应用程序的窗口
int main()
{
    const char*ImageName="蓝天白云.jpg";
    const char*WindowsTitle="ImageDisplay";
    //加载图片至内存
    IplImage* img=cvLoadImage(ImageName);
    //创建窗口
    cvNamedWindow(WindowsTitle,0);
    //显示图片
    cvShowImage(WindowsTitle,img);
    //使程序暂停，等待触发按键操作
    cvWaitKey(0);
    //释放内存
    cvReleaseImage(&img);
    //销毁窗口
    cvDestroyWindow(WindowsTitle);
}
```

# 关键函数

## cvLoadImage

**函数原型**：`CVAPI(IplImage*) cvLoadImage( const char* filename, int iscolor CV_DEFAULT(CV_LOAD_IMAGE_COLOR))` 
**函数说明**：从文件中读取图像，加载至内存。 
`filename`：要被读入的文件名。 
`iscolor`：指定读入图像的颜色。默认为`CV_LOAD_IMAGE_COLOR`，也即彩色。 
`IplImage`：来源于Intel Image Processing Library函数库，该结构体包含了一系列图像信息，是整个OpenCV函数库的基础。 
该函数执行完后将返回一个指针，此指针指向一块为描述该图像文件的数据结构(IplImage*)而分配的内存块。

## cvNamedWindow

**函数原型**：`CVAPI(int) cvNamedWindow( const char* name, int flags CV_DEFAULT(CV_WINDOW_AUTOSIZE) ) `
**函数功能**：创建窗口。 
`name`:窗口的名称，用来区分不同的窗口，并被显示为窗口标题。 
`flags`:窗口属性标志。当其为`CV_WINDOW_NORMAL(0)`时，则表示可以手动调整窗口大小；当其为`CV_WINDOW_AUTOSIZE(1)`时，则表示会根据图像自动调整窗口大小，不能手动调整；当其为`CV_WINDOW_OPENGL`，则表示窗口支持opengl。

## cvShowImage

**函数原型**：`CVAPI(void) cvShowImage( const char* name, const CvArr* image ) `
**函数功能**：在指定窗口中显示图像。 
`name`:窗口的名字。 
`image`：被显示的图像。

## cvWaitKey

**函数原型**：`CVAPI(int) cvWaitKey(int delay CV_DEFAULT(0))` 
**函数功能**：等待按键事件。 
`delay`:延迟的毫秒数。当delay<=0时，表示无限制的等待按键事件；否则延迟“delay”毫秒。返回值为被按键的值，如果超过指定时间则返回-1.

## cvReleaseImage

**函数原型**：`CVAPI(void) cvReleaseImage( IplImage** image ) `
**函数功能**：释放该图像文件所分配的内存。

## cvDestroyWindow

**函数原型**：`CVAPI(void) cvDestroyWindow( const char* name ) `
**函数功能**：销毁窗口。

# IplImage

IplImage结构来自于Intel Image Processing Library。OpenCV只支持其中的一个子集。 
以下就是IplImage结构体的定义：

```C++
typedef struct _IplImage
{
int nSize; /* 图像结构大小 */
int ID;    /* 版本 (=0)*/
int nChannels;    /* 大多数OPENCV函数支持1,2,3 或 4 个通道 */
int alphaChannel; /* 被OpenCV忽略 */
int depth; /* 像素的位深度: IPL_DEPTH_8U, IPL_DEPTH_8S, IPL_DEPTH_16U,
IPL_DEPTH_16S, IPL_DEPTH_32S, IPL_DEPTH_32F and IPL_DEPTH_64F 可支持 */
char colorModel[4]; /* 被OpenCV忽略 */
char channelSeq[4]; /* 同上 */
int dataOrder;      /* 0 - 交叉存取颜色通道, 1 - 分开的颜色通道.
cvCreateImage只能创建交叉存取图像 */
int origin; /* 0 - 顶—左结构,
               1 - 底—左结构 (Windows bitmaps 风格) */
int align;  /* 图像行排列 (4 or 8). OpenCV 忽略它，使用 widthStep 代替 */
int width;  /* 图像宽像素数 */
int height; /* 图像高像素数*/
struct _IplROI *roi;  /* 图像感兴趣区域. 当该值非空只对该区域进行处理 */
struct _IplImage *maskROI; /* 在 OpenCV中必须置NULL */
void *imageId; /* 同上*/
struct _IplTileInfo *tileInfo; /*同上*/
int imageSize; /* 图像数据大小(在交叉存取格式下imageSize=image->height*image->widthStep），单位字节*/
char *imageData; /* 指向排列的图像数据 */
int widthStep;   /* 排列的图像行大小，以字节为单位 */
int BorderMode[4];  /* 边际结束模式, 被OpenCV忽略 */
int BorderConst[4]; /* 同上 */
char *imageDataOrigin; /* 指针指向一个不同的图像数据结构（不是必须排列的），是为了纠正图像内存分配准备的 */
}
IplImage;
```