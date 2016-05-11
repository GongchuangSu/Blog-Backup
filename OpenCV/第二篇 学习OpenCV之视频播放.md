title: 第二篇 学习OpenCV之视频播放
layout: post
date: 2015-06-21 11:30:13  
comments: true
type: "categories"
categories: 
- OpenCV
tags: 
- OpenCV
- 视频播放

---
使用OpenCV播放视频，就是将视频中的每一帧顺序的读取出来。 
例1：一个简单的OpenCV程序，用于播放事先存储在特定位置的视频文件
```C++
#include "highgui.h"
#pragma comment(linker, "/subsystem:\"windows\" /entry:\"mainCRTStartup\"")  //用于屏蔽控制台应用程序的窗口
int main()
{
    const char*AviName="MV.mp4";
    const char*WindowsTitle="MV";
    cvNamedWindow(WindowsTitle,0);
    CvCapture*capture=cvCreateFileCapture(AviName);
    IplImage*frame;
    while (1)
    {
        frame=cvQueryFrame(capture);
        if(!frame)
            break;
        cvShowImage(WindowsTitle,frame);
        char c=cvWaitKey(33);  //当前帧被显示后，等待33ms，如果其间触发了一个按键，则返回该键的ASCII码值；否则，返回-1
        if(c==27)  //如果用户触发了ESC键（ASCII 27），跳出循环
            break;
    }
    cvReleaseCapture(&capture);
    cvDestroyWindow("WindowsTitle");
}
```
<!--more-->

# 关键函数

## cvCreateFileCapture

**函数原型**：`CVAPI(CvCapture*) cvCreateFileCapture( const char* filename )` 
**函数说明**：初始化从视频文件获取帧。其中，`filename`为视频文件名。通过参数设置确定要读入的视频文件，返回一个`CvCapture`结构的指针。该结构包含了关于要读入视频文件的所有信息。当分配的结构不再使用时，应当使用函数`cvReleaseCapture`释放掉。

## cvQueryFrame

**函数原型**：`IplImage* cvQueryFrame( CvCapture* capture )` 
**函数说明**：从摄像头或者文件中抓取一帧，然后解压并返回这一帧。返回的图像不可以被用户释放或修改。抓取后，`capture`被指向下一帧，可用`cvSetCaptureProperty`调整`capture`到合适的帧。如果没有帧可以被获取（摄像头断开或文件中的帧已读完），函数返回`NULL`。这个函数可以看做是函数`cvGrabFrame`和函数`cvRetrieveFrame`的组合。

## cvSetCaptureProperty

**函数原型**：`int cvSetCaptureProperty(CvCapture* capture, int property_id, double value) `
**函数说明**：设置指定获取视频的属性。 
`capture`：获取视频结构。 
`property_id`：属性标识符。可以是下面参数之一。 
`CV_CAP_PROP_POS_MSEC` - 从文件开始的位置，单位为毫秒 
`CV_CAP_PROP_POS_FRAMES` - 单位为帧数的位置（只对视频文件有效） 
`CV_CAP_PROP_POS_AVI_RATIO` - 视频文件的相对位置（0 - 影片的开始，1 - 影片的结尾) 
`CV_CAP_PROP_FRAME_WIDTH` - 视频流的帧宽度（只对摄像头有效） 
`CV_CAP_PROP_FRAME_HEIGHT` - 视频流的帧高度（只对摄像头有效） 
`CV_CAP_PROP_FPS` - 帧率（只对摄像头有效） 
`CV_CAP_PROP_FOURCC` - 表示codec的四个字符（只对摄像头有效） 
`value`：属性的值。

## cvGetCaptureProperty

**函数原型**：`double cvGetCaptureProperty(CvCapture* capture, int property_id) `
**函数说明**：获得摄像头或视频文件的指定属性。当查询不支持所使用的VideoCapture类后端的属性时，返回0。 
`capture`：获取视频结构。 
`property_id`：属性标识符。可以是下面参数之一。 
`CV_CAP_PROP_POS_MSEC` - 影片目前位置，为毫秒数或者视频获取时间戳 
`CV_CAP_PROP_POS_FRAMES` - 将被下一步解压／获取的帧索引，以0为起点 
`CV_CAP_PROP_POS_AVI_RATIO` - 视频文件的相对位置（0 - 影片的开始，1 - 影片的结尾) 
`CV_CAP_PROP_FRAME_WIDTH` - 视频流中的帧宽度 
`CV_CAP_PROP_FRAME_HEIGHT` - 视频流中的帧高度 
`CV_CAP_PROP_FPS` - 帧率 
`CV_CAP_PROP_FOURCC` - 表示codec的四个字符 
`CV_CAP_PROP_FRAME_COUNT` - 视频文件中帧的总数

## cvGrabFrame

**函数原型**：`int cvGrabFrame(CvCapture* capture)` 
**函数说明**：从摄像头或视频文件中抓取帧。主要用于快速的抓取帧，被抓取的帧存储在视频获取结构中。在多个摄像机环境下，特别是摄像机没有硬件同步功能时显得很重要。由于被抓取的帧可能是压缩格式（由摄像头/驱动定义），这时需要调用函数`cvRetrieveFrame`来解码并获取帧。

## cvRetrieveFrame

**函数原型**：`IplImage* cvRetrieveFrame(CvCapture* capture, int streamIdx=0 ) `
**函数说明**：解码并返回刚才被抓取的帧。如果没有帧被抓取（摄像头断开或文件中的帧已读完），函数返回`NULL`。函数返回的帧存储在视频获取结构中。返回的图像不可以被用户释放或修改。

# CvCapture

`CvCapture`是一种视频获取结构体，用来保存捕获图像的相关信息，在处理视频时，让程序一帧帧的读取并更新。没有公共接口，只能用来作为视频获取函数的一个参数。

上面的例程只是简单的利用OpenCV实现视频的播放，并不能对视频的播放进度进行控制。下面，将编程实现添加进度条到基本浏览窗口，进度条可显示并控制视频播放的进度。 
例2：

```c++
// 作者：GongchuangSu ( http://gongchuangsu.com/ )
// 时间：2015.06.21                          
// 说明：通过创建进度条显示并控制视频播放的进度。实质上，是通过添加一个全局
//      变量（获取视频结构的指针g_capture ）来表示进度条位置并添加一个回
//      调函数更新变量以及重新设置视频读入的位置。                                        

#include "cv.h"
#include "highgui.h"
#pragma comment(linker, "/subsystem:\"windows\" /entry:\"mainCRTStartup\"")  //用于屏蔽控制台应用程序的窗口

int g_slider_position=0;
CvCapture* g_capture=NULL;

//定义回调函数
void onTrackbarSlide(int pos){
    cvSetCaptureProperty(
        g_capture,
        CV_CAP_PROP_POS_FRAMES,
        pos
        );
}

int main(){
    const char*AviName="MV.mp4";
    const char*WindowsTitle="视频播放控制";

    cvNamedWindow(WindowsTitle,0);
    g_capture = cvCreateFileCapture( AviName );

    //查询视频文件的总帧数
    int frames = (int) cvGetCaptureProperty(
        g_capture,
        CV_CAP_PROP_FRAME_COUNT
        );

    if( frames!= 0 ){
        IplImage* frame;
        while(1){
            frame=cvQueryFrame(g_capture);
            if(!frame) break;
            //取得将被获取帧的帧数（第N帧）
            int g_slider_position = (int)cvGetCaptureProperty(
                g_capture,
                CV_CAP_PROP_POS_FRAMES
                );
            //创建进度条，实时显示并控制视频播放进度
            cvCreateTrackbar(
                "进度条",
                WindowsTitle,
                &g_slider_position,
                frames,
                onTrackbarSlide
                );
            //显示图像
            cvShowImage(WindowsTitle,frame);
            char c=cvWaitKey(33);
            if( c ==27) break;
    }
    }
    //释放内存
    cvReleaseCapture(&g_capture);
    //销毁窗口
    cvDestroyWindow(WindowsTitle);
    return(0);
}
```

**整个编程实现关键在于函数`cvCreateTrackbar`的使用，以下是该函数的原型和说明。** 
**函数原型**：`int cvCreateTrackbar(const char* trackbar_name, const char* window_name, int* value, int count,CvTrackbarCallback on_change=NULL )`
**函数说明**：创建`trackbar`并将它添加到指定的窗口，同时设置回调函数。 
`trackbar_name`：被创建的进度条名称。 
`window_name`：窗口名字，这个窗口将为被创建`trackbar`的父对象。 
`value`：整型变量指针，其值反应进度条的位置。 
`count`：进度条的最大值。最小值总为`0`。 
`on_change`：每次进度条改变位置时，函数指针将被调用。这个函数的原型应为`void Foo（int）`，其函数中的参数是进度条改变后的位置（内部自动获取）。获取进度条改变后的位置后，然后再更新全局变量`g_capture`，读取的帧便从改变后的位置开始。