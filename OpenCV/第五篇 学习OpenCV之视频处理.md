title: 第五篇 学习OpenCV之视频处理
layout: post
date: 2015-07-29 19:39:21  
comments: true
type: "categories"
categories: 
- OpenCV
tags: 
- OpenCV
- 视频处理

---

**源代码：**
<!--more-->
```c++
// 作者：GongchuangSu ( http://gongchuangsu.com/ )
// 时间：2015.07.26
// 说明：从视频文件中读入数据，将读入数据转换为灰度图，对图像做Canny边缘检测。
//       将上面三个步骤实现显示在一个图像中，并在图像的三个不同部分写上合适的
//       标签,同时在这幅图像中任何地方点击鼠标，将当前图像的坐标显示于点击鼠标处。
// 注意：当R = G = B时为灰度色。

#include "cv.h"
#include "highgui.h"
#include <iostream>
using namespace std;
//#pragma comment(linker, "/subsystem:\"windows\" /entry:\"mainCRTStartup\"")
int col = 0;
int row = 0;
char *str = (char *)malloc(30*sizeof(char *));
// 定义 Canny 边缘检测函数
IplImage* doCanny(
    IplImage* in,
    int lowThresh,
    int highThresh,
    int aperture
    ){
        if( in->nChannels != 1)  // Canny只能处理灰度图片
            return(0);
        IplImage* out = cvCreateImage(
            cvGetSize( in ),
            in->depth,
            in->nChannels
            );
        cvCanny( in, out, lowThresh, highThresh, aperture );
        return( out );
};
// 定义鼠标回调函数
void my_mouse_callback( int event, int x, int y, int flags, void* param){
    if( event == CV_EVENT_LBUTTONDOWN){
        col = x;
        row = y;
    }
}
int main()
{
    // 定义 Canny 边缘检测的阈值
    int thresh = 30;
    // 定义字体相关属性
    CvFont font;
    cvInitFont( &font, CV_FONT_HERSHEY_PLAIN, 1, 1, 0, 1, 8);
    const char *AviName="MV.mp4";
    const char *WindowsTitle = "Merge";
    cvNamedWindow( WindowsTitle, 1);
    CvCapture *capture=cvCreateFileCapture(AviName);
    // 取得视频文件的大小并输出 
    CvSize size = cvSize(
        (int)cvGetCaptureProperty( capture, CV_CAP_PROP_FRAME_WIDTH),
        (int)cvGetCaptureProperty( capture, CV_CAP_PROP_FRAME_HEIGHT)
        );
    // 创建图像 
    IplImage *src = cvCreateImage( size, IPL_DEPTH_8U, 3);
    IplImage *dst_gray = cvCreateImage( size, IPL_DEPTH_8U, 1);
    IplImage *dst_canny = cvCreateImage( size, IPL_DEPTH_8U, 1);
    IplImage *dst_merge = cvCreateImage( cvSize( size.width*3, size.height), IPL_DEPTH_8U, 3);
    // 创建三个图像头，分别指向图像 dst_merge 的开始处、1/3处、2/3处
    IplImage *src_header = cvCreateImageHeader( size, IPL_DEPTH_8U, 3);
    src_header->origin = dst_merge->origin;
    src_header->widthStep = dst_merge->widthStep;
    src_header->imageData = dst_merge->imageData;
    IplImage *dst_gray_header = cvCreateImageHeader( size, IPL_DEPTH_8U, 3);
    dst_gray_header->origin = dst_merge->origin;
    dst_gray_header->widthStep = dst_merge->widthStep;
    dst_gray_header->imageData = dst_merge->imageData + dst_merge->widthStep/3;
    IplImage *dst_canny_header = cvCreateImageHeader( size, IPL_DEPTH_8U, 3);
    dst_canny_header->origin = dst_merge->origin;
    dst_canny_header->widthStep = dst_merge->widthStep;
    dst_canny_header->imageData = dst_merge->imageData + dst_merge->widthStep*2/3;
    // 创建图像头 dst_gray_header 的单通道 r ，g ，b 图像
    IplImage *dst_gray_header_r = cvCreateImage( cvGetSize(dst_gray_header), dst_gray_header->depth, 1);
    IplImage *dst_gray_header_g = cvCreateImage( cvGetSize(dst_gray_header), dst_gray_header->depth, 1);
    IplImage *dst_gray_header_b = cvCreateImage( cvGetSize(dst_gray_header), dst_gray_header->depth, 1);
    // 创建图像头 dst_canny_header 的单通道 r ，g ，b 图像
    IplImage *dst_canny_header_r = cvCreateImage( cvGetSize(dst_canny_header), dst_canny_header->depth, 1);
    IplImage *dst_canny_header_g = cvCreateImage( cvGetSize(dst_canny_header), dst_canny_header->depth, 1);
    IplImage *dst_canny_header_b = cvCreateImage( cvGetSize(dst_canny_header), dst_canny_header->depth, 1);
    // 读取视频并转换
    while( (src = cvQueryFrame(capture)) != NULL )
    {
        // 将原始图像拷贝给指向合并图像开始处的图像头
        cvCopy( src, src_header);
        // 将原始图像转换为灰度图像
        cvConvertImage( src, dst_gray);
        // 将灰度图像头分割成红、绿、蓝三个单通道图像，并将灰度图像拷贝给三个单通道图像，然后再将三个单通道
        // 合并，以使灰度图像头显示的是灰度图像。
        cvZero(dst_gray_header);
        cvSplit( dst_gray_header, dst_gray_header_r, dst_gray_header_g, dst_gray_header_b, 0);
        cvCopy( dst_gray, dst_gray_header_r);
        cvCopy( dst_gray, dst_gray_header_g);
        cvCopy( dst_gray, dst_gray_header_b);
        cvMerge( dst_gray_header_r, dst_gray_header_g, dst_gray_header_b, 0, dst_gray_header);
        // 将灰度图像进行 Canny 边缘检测处理
        dst_canny = doCanny( dst_gray, thresh, 3*thresh, 3);
        cvZero(dst_canny_header);
        cvSplit( dst_canny_header, dst_canny_header_r, dst_canny_header_g, dst_canny_header_b, 0);
        cvCopy( dst_canny, dst_canny_header_r);
        cvCopy( dst_canny, dst_canny_header_g);
        cvCopy( dst_canny, dst_canny_header_b);
        cvMerge( dst_canny_header_r, dst_canny_header_g, dst_canny_header_b, 0, dst_canny_header);
        // 在合并图像的三个不同处显示相应的文字
        cvPutText( dst_merge, "Color", cvPoint(315,25), &font, cvScalar( 255, 255, 255));
        cvPutText( dst_merge, "Gray", cvPoint(315*3,25), &font, cvScalar( 255, 255, 255));
        cvPutText( dst_merge, "Canny", cvPoint(315*5,25), &font, cvScalar( 255, 255, 255));
        cvShowImage( WindowsTitle, dst_merge);
        // 设置鼠标回调函数
        cvSetMouseCallback(
            WindowsTitle,
            my_mouse_callback,
            (void *)dst_merge
            );
        // 输出坐标值
        cout<<"( x, y) = "<<"( "<<col<<", "<<row<<")"<<endl;
        memset( str, 0, 30);
        // 将格式化的数据写入字符串缓冲区
        sprintf( str, "( %d, %d)",col,row);
        // 将字符串显示在相应的图像位置
        cvPutText( dst_merge, str, cvPoint(col,row), &font, cvScalar( 0, 0, 255)); 
        cvShowImage(WindowsTitle,dst_merge);
        // 等待按键触发
        char c = cvWaitKey(33);
        if( c == 27)
            break;
    }
    // 释放资源，销毁窗口
    cvReleaseCapture(&capture);
    cvReleaseImage(&dst_merge);
    cvReleaseImage(&src);
    cvReleaseImage(&dst_gray);
    cvReleaseImage(&dst_canny);
    cvReleaseImageHeader(&src_header);
    cvReleaseImageHeader(&dst_gray_header);
    cvReleaseImageHeader(&dst_canny_header);
    cvReleaseImage(&dst_gray_header_r);
    cvReleaseImage(&dst_gray_header_g);
    cvReleaseImage(&dst_gray_header_b);
    cvReleaseImage(&dst_canny_header_r);
    cvReleaseImage(&dst_canny_header_g);
    cvReleaseImage(&dst_canny_header_b);
    cvDestroyAllWindows();
    return(0);
}
```
**运行结果：**
![](http://i.imgur.com/QUBJ2EE.jpg)
