title: 第三篇 学习OpenCV之图像变换（3）
layout: post
date: 2015-07-05 10:21:25  
comments: true
type: "categories"
categories: 
- OpenCV
tags: 
- OpenCV
- 图像变换

---
# 序言
在本篇前两节中，我们学习了对图像进行简单的高斯模糊处理以及Canny边缘检测，它们处理的都是从其他地方载入的固定图像。而在更多的情况下，我们需要处理从摄像机或某视频文件中实时读入的视频流。HighGUI做了很多工作才使得摄像机图像序列看起来像一个视频文件，当我们需要处理摄像机或视频文件中的图像序列时我们只需要简单地从摄像机或视频文件获得图像，像图像一样处理。 
接下来，我们将编程实现读入一个彩色视频文件，并以灰度格式输出这个视频文件，同时将每一帧图像转换为对数极坐标格式，输出并写入新的视频文件中。 

<!--more-->

**源程序：**
```c++
// 作者：GongchuangSu ( http://gongchuangsu.com/ )
// 时间：2015.07.05                          
// 说明：打开一个视频文件，读取文件内容，将每一帧图像转换为
//      对数极坐标格式和灰度格式，并将转换后的图像序列写入
//      新的视频文件中，并输出。

#include "cv.h"
#include "highgui.h"
//#pragma comment(linker, "/subsystem:\"windows\" /entry:\"mainCRTStartup\"")  //用于屏蔽控制台应用程序的窗口
int main(){
    /* 定义视频输入输出名称 */
    const char *VideoName_Input = "MV.mp4";
    const char *VideoName_Output = "logpolar.mp4";
    /* 创建窗口 */
    cvNamedWindow( "原视频", CV_WINDOW_AUTOSIZE );
    cvNamedWindow( "logpolar", CV_WINDOW_AUTOSIZE );
    cvNamedWindow( "gray", CV_WINDOW_AUTOSIZE );
    /* 读取文件 */
    CvCapture* capture = 0;
    capture = cvCreateFileCapture( VideoName_Input );
    if(!capture){
        return -1;
    }
    IplImage* bgr_frame = cvQueryFrame(capture);
    /* 取得视频文件的帧数并输出 */
    double fps = cvGetCaptureProperty(
        capture,
        CV_CAP_PROP_FPS
    );
    printf("fps=%d\n",(int)fps);
    /* 取得视频文件的大小并输出 */
    CvSize size = cvSize(
        (int)cvGetCaptureProperty( capture, CV_CAP_PROP_FRAME_WIDTH),
        (int)cvGetCaptureProperty( capture, CV_CAP_PROP_FRAME_HEIGHT)
    );
    printf("frame (w, h) = (%d, %d)\n",size.width,size.height);
    /* 创建视频文件读入器 */
    CvVideoWriter* writer = cvCreateVideoWriter(
        VideoName_Output,
        CV_FOURCC('M','J','P','G'),
        fps,
        size
        );
    /* 为logpolar创建图像 */
    IplImage* logpolar_frame = cvCreateImage(
        size,
        IPL_DEPTH_8U,
        3
    );
    /* 为gray创建图像 */
    IplImage* gray_frame = cvCreateImage(
        size,
        IPL_DEPTH_8U,
        1
        );
    /* 图像转换并显示 */
    while( (bgr_frame = cvQueryFrame(capture)) != NULL ){
        cvShowImage("原视频", bgr_frame );
        /* 将原图像格式转换为灰度格式并显示 */
        cvConvertImage(  
            bgr_frame,
            gray_frame
            );
        cvShowImage("gray", gray_frame );
        /* 将原图像格式转换为对数极坐标格式并显示 */
        cvLogPolar( 
            bgr_frame,
            logpolar_frame,
            cvPoint2D32f(bgr_frame->width/2,bgr_frame->height/2),
            40,
            CV_INTER_LINEAR+CV_WARP_FILL_OUTLIERS 
            );
        cvShowImage("logpolar", logpolar_frame );
        /* 将对数极坐标格式的图像写入指定视频文件中 */
        cvWriteFrame( writer, logpolar_frame );
        /* 等待按键触发 */
        char c = cvWaitKey(10);
        if( c == 27 ) break;
    }
    /* 释放资源 */
    cvReleaseVideoWriter( &writer );
    cvReleaseImage( &gray_frame );
    cvReleaseImage( &logpolar_frame );
    cvReleaseCapture( &capture );
    return(0);
}
```

# 相关函数
## cvCreateVideoWriter

**函数原型**：`CvVideoWriter* cvCreateVideoWriter(const char* filename, int fourcc, double fps, CvSize frame_size,int is_color=1 )` 
**函数说明**：创建视频文件写入器结构。 
`filename`：输出视频文件名称。 
`fourcc`：四个字符用来表示压缩帧的`codec` 例如，`CV_FOURCC``(‘P’,’I’,’M’,’1’)`是`MPEG-1 codec`， `CV_FOURCC``(‘M’,’J’,’P’,’G’)`是`motion-jpeg codec`等。 在Win32下，如果传入参数-1，可以从一个对话框中选择压缩方法和压缩参数。具体可参考http://www.fourcc.org/codecs.php 
`fps`：被创建视频流的帧率。 
`frame_size`：视频流的大小。 
`is_color`：如果非零，编码器将希望得到彩色帧并进行编码；否则，是灰度帧（只有在Windows下支持这个标志）。 默认为1。

## cvConvertImage

**函数原型**：`void cvConvertImage( const CvArr* src, CvArr* dst, int flags=0 )` 
**函数说明**：把一幅图像转换为另外一幅图像，并可以选择同时对其进行垂直翻转 。 
`src`：输入图像。 
`dst`：目标图像。必须为单通道或者3通道8位图像。 
`flags`：操作标志。 
`CV_CVTIMG_FLIP` ： 垂直翻转图像。 
`CV_CVTIMG_SWAP_RB` ：交换红蓝通道。在OpenCV中，彩色图像的通道顺序是 `BGR` 然而在一些系统中，在显示图像之前通道顺序应该被翻转（`cvShowImage`能够自动转换）。

## cvLogPolar

**函数原型**：`void cvLogPolar(const CvArr* src, CvArr* dst, CvPoint2D32f center, double M, int flags=CV_INTER_LINEAR+CV_WARP_FILL_OUTLIERS )` 
**函数说明**：把图像映射到对数极坐标空间。此函数模仿人类视网膜中央凹视力，并且对于目标跟踪等可用于快速尺度和旋转变换不变模板匹配。 
`src`：输入图像。 
`dst`：输出图像。 
`center`：变换的中心，输出图像在此处最精确。 
`M`：幅度的尺度参数。 
`flags`：插值方法。

## cvWriteFrame

**函数原型**：`int cvWriteFrame(CvVideoWriter* writer, const IplImage* image)` 
**函数说明**：写入一帧到一个视频文件中 
`writer`：视频文件写入器结构。 
`image`：被写入的帧。

## 运行结果
![](http://i.imgur.com/NpsP1Md.jpg)