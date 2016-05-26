title: 第四篇 学习OpenCV之访问图像数据
layout: post
date: 2015-07-25 09:18:21  
comments: true
type: "categories"
categories: 
- OpenCV
tags: 
- OpenCV
- 图像数据

---
# 序言
这一篇，我们主要学习OpenCV矩阵和图像类型以及如何访问矩阵和图像数据。 
![](http://i.imgur.com/7rZH9lK.jpg)
在开始探讨图像细节之前，我们需要先了解另一种数据类型`CvMat`，OpenCV的矩阵结构。虽然OpenCV完全由C语言实现，但`CvMat`和`IplImage`之间的关系就如同C++中的继承关系。实质上，`IplImage`可以被视为从`CvMat`中派生的。因此，在试图了解复杂的派生类之前，最好先了解基本的类。第三个类`CvArr`，可以被视为一个抽象基类，`CvMat`由它派生。

<!--more-->

# CvMat矩阵结构

## 矩阵头
```c++
typedef struct CvMat
{
    int type; // 矩阵元素的数据类型
    int step; // 全行的字节长度（单位为字节）

    /* for internal use only */
    int* refcount;
    int hdr_refcount;

    union
    {
        uchar* ptr; // pointer to 8-bit unsigned elements
        short* s;   // pointer to 16-bit signed elements
        int* i;     // pointer to 32-bit signed elements
        float* fl;  // pointer to 32-bit floating-point elements
        double* db; // pointer to 64-bit floating-point elements
    } data; // Pointers to the actual matrix data

#ifdef __cplusplus
    union
    {
        int rows;
        int height;
    };

    union
    {
        int cols;
        int width;
    };
#else
    int rows;
    int cols;
#endif

} CvMat;
```
**说明**： 

1. 矩阵的元素`data`是一个联合体，它是指向实际矩阵数据的指针。对这个指针解引用的时候，必须指明结构体中的正确的元素以便得到正确的指针类型。 
2. `CvMat`结构中为了兼容`IplImage`结构，有宽度和高度的概念，这个概念已经被最新的行和列取代。

## 矩阵数据的存取
访问矩阵中的数据有三种方法：简单的方法、麻烦的方法和恰当的方法。 
### 简单的方法 
从矩阵中得到一个元素的最简单的方法是利用`CV_MAT_ELEM()`和`CV_MAT_ELEM_PTR()`宏。
**例1：**
```c++
// 作者：GongchuangSu ( http://gongchuangsu.com/ )
// 时间：2015.07.20
// 说明：利用CV_MAT_ELEM()和CV_MAT_ELEM_PTR()宏存取矩阵数据

#include <cv.h>
#include <highgui.h>
//#pragma comment(linker, "/subsystem:\"windows\" /entry:\"mainCRTStartup\"")
int main( int argc, char * argv[]){
    // 创建矩阵
    CvMat *srcMat = cvCreateMat( 250, 250, CV_32FC1);
    printf("srcMat->step = %d\n",srcMat->step);
    printf("srcMat->width = %d\n",srcMat->width);
    // 清空数组
    cvZero(srcMat);
    // 利用CV_MAT_ELEM()宏返回提取出的元素的值
    float element_125_125 = CV_MAT_ELEM( *srcMat, float, 125, 125);
    printf("Before set: elemrnt_125_125 = %f\n",element_125_125);
    // 利用CV_MAT_ELEM_PTR()宏为矩阵的某个元素设置一个数值
    element_125_125 = 7.7;
    *((float *)CV_MAT_ELEM_PTR( *srcMat, 125, 125)) = element_125_125;
    // 利用CV_MAT_ELEM_PTR()宏返回提取出的元素的值
    element_125_125 = *((float *)CV_MAT_ELEM_PTR( *srcMat, 125, 125));
    printf("After set: elemrnt_125_125 = %f\n",element_125_125);
    // 创建窗口，显示矩阵
    cvNamedWindow("Easy way");
    cvShowImage("Easy way", srcMat);
    // 等待按键，释放内存
    cvWaitKey(0);
    cvReleaseMat(&srcMat);
    cvDestroyWindow( "Easy way" );
    return 0;
}
```
**运行结果： **
![](http://i.imgur.com/nAtNjZm.jpg)
![](http://i.imgur.com/RfYVlEK.jpg)
**说明：** 
1. `CV_MAT_ELEM()`宏原型：`CV_MAT_ELEM( mat, elemtype, row, col )`
`mat`：传入的矩阵。 
`elemtype`：待提取的元素的类型。 
`row`：行数。 
`col`：列数

2. `CV_MAT_ELEM_PTR()`宏原型：`CV_MAT_ELEM_PTR( mat, row, col )`
参数说明同上。
3. 这两个宏最重要的区别是后者在指针解引用之前将其转化成指定的类型。如果需要同时读取数据和设置数据，可以直接调用`CV_MAT_ELEM_PTR()`。但在这种情况下，必须自己将指针转化成恰当的类型（具体可通过程序来体会）。 
4. 通过程序可以看出，这些宏在每次调用时都重新计算指针，首先要查找指向矩阵基本元素数据区的指针、计算目标数据在矩阵中的相对地址，然后将相对位置与基本位置相加。所以这种方法虽然简单直接，但不是存取矩阵数据的最佳方法。在计划顺序访问矩阵中的所有元素时，这种方法的缺点尤为突出。

### 麻烦的方法 
简单的方法”中讨论的两个宏仅仅适用于访问1维或2维的数组。事实上，OpenCV可以支持普通的N维的数组，这个N值可以取值为任意大的数。 
为了访问普通矩阵中的数据，我们可以利用`cvPtr*D`和`cvGet*D`…等函数族。 
**例2：**
```c++
// 作者：GongchuangSu ( http://gongchuangsu.com/ )
// 时间：2015.07.20
// 说明：利用函数族cvPtr*D遍历矩阵，对矩阵元素的通道值进行相关的设置

#include <cv.h>
#include <highgui.h>
#include <iostream>
using namespace std;

int main( int argc, char * argv[]){
    // 创建矩阵：行数->250 列数->250 元素类型->32位浮点型三通道
    CvMat *srcMat = cvCreateMat( 250, 250, CV_32FC3);
    // 清空数组
    cvSetZero(srcMat);
    // 显示矩阵的行数据长度（字节）、宽度（元素）、高度（元素）
    cout << "srcMat->step = " << srcMat->step << endl;
    cout << "srcMat->rows = " << srcMat->rows << endl;
    cout << "srcMat->cols = " << srcMat->cols << endl;
    // 循环扫描、设置
    int row = 0, col = 0;
    for( row = 0; row < srcMat->rows; row++){
        for( col = 0; col < srcMat->cols; col++){
            // +0 -> B; +4 -> G; +8 -> R
            *((float *)(cvPtr2D( srcMat, row, col, NULL) + 0 )) = 255;
            *((float *)(cvPtr2D( srcMat, row, col, NULL) + 4 )) = 0;
            *((float *)(cvPtr2D( srcMat, row, col, NULL) + 8 )) = 0;
        }
    }
    // 创建窗口，显示矩阵
    cvNamedWindow("Hard way");
    cvShowImage("Hard way", srcMat);
    // 等待按键，释放内存
    cvWaitKey(0);
    cvReleaseMat(&srcMat);
    cvDestroyWindow( "Hard way" );
    return 0;
}
```
**运行结果：**
![](http://i.imgur.com/CDXIPZ5.jpg)
![](http://i.imgur.com/jhAYOGX.jpg)

说明： 
1. `cvPtr2D`函数原型：`uchar* cvPtr2D(const CvArr* arr, int idx0, int idx1, int* type=NULL)`
其中，`arr`指输入的矩阵，`idx0`指元素的行值，`idx1`指元素的列值，`type`指矩阵元素类型输出参数。函数`cvPtr2D` 返回指向特殊数组元素的指针。 
**特别注意：**默认情况下，函数返回的是指向uchar类型的指针，当矩阵元素不是`uchar`类型时，应将指针所指向元素的类型转化为恰当的类型。如：当矩阵元素类型为`CV_32FC3`，函数应转化为`(float *) cvPtr2D(const CvArr* arr, int idx0, int idx1, int* type=NULL)`。 
2. 默认情况下，函数返回的指针的类型（而不是指针指向的数据的类型） 是`uchar`型，占一个字节。当矩阵元素类型为`CV_32FC3` 时，要想使指针从一个通道指向另一个通道，需在原先指针的基础上加4（因为每个通道占4个字节）。 
3. 对于N维矩阵，可以利用函数`cvPtrND()`实现。这些指针函数访问矩阵中的特定的点，然后由这个点出发，用指针的算术运算得到指向矩阵中的其他数据的指针。需要了解的是，在多通道的矩阵中，通道是连续的，而且矩阵数据如下存储：`bgrbgrbgr…`

### 恰当的方法 
上面两种方法基本可以满足矩阵中数据的访问，当大多数时候，计算机视觉是一种运算密集型的任务，因而要尽量利用最有效的方法做事。由上面例子可以看出，通过这些函数接口是不可能做到十分高效的。相反地，应该定义自己的指针计算并且在矩阵中利用自己的方法。如果打算对数组中的每一个元素执行一些操作，使用自己的指针是尤为重要的。 
**例3：**
```c++
// 作者：GongchuangSu ( http://gongchuangsu.com/ )
// 时间：2015.07.21
// 说明：通过指向实际矩阵数据的一些指针（联合体data）来直接访问
//       矩阵中的数据。

#include <cv.h>
#include <highgui.h>
#include <iostream>
using namespace std;

int main( int argc, char * argv[]){
    CvMat *srcMat = cvCreateMat( 250, 250, CV_8UC3);
    // 清空数组
    cvZero(srcMat);
    // 显示矩阵的行数据长度（字节）、宽度（元素）、高度（元素）
    cout << "srcMat->step = " << srcMat->step << endl;
    cout << "srcMat->width = " << srcMat->width << endl;
    cout << "srcMat->height = " << srcMat->height << endl;
    // 循环访问、设置
    for( int row = 0; row < srcMat->rows; row++){
        uchar *ptr = ( uchar *)( srcMat->data.ptr + row * srcMat->step );
        for( int col = 0; col < srcMat->cols; col++){
            // +0 -> B; +1 -> G; +2 -> R
            ptr[ 3*col ] = 255;
            ptr[ 3*col + 1 ] = 0;
            ptr[ 3*col + 2 ] = 0;
        }
    }
    // 创建窗口，显示矩阵
    cvNamedWindow("Right way");
    cvShowImage("Right way", srcMat);
    // 等待按键，释放内存
    cvWaitKey(0);
    cvReleaseMat(&srcMat);
    cvDestroyWindow( "Right way" );
    return 0;
}
```
**运行结果：**
![](http://i.imgur.com/iM6CswZ.jpg)
![](http://i.imgur.com/KhLUJFH.jpg)

**说明： **
1. 为了使指针产生正确的偏移，必须用矩阵的行数据长度元素（`step`），上面已提到，行数据元素是用字节来计算的。为了安全，指针最好用字节计算，然后分配恰当的类型。 
2. 若矩阵数据类型设置为`CV_32FC3`，那么在循环访问时指针指向的数据类型应设置为对应的浮点型。上面的程序需做如下修改：
```c++
CvMat *srcMat = cvCreateMat( 250, 250, CV_32FC3);
for( int row = 0; row < srcMat->rows; row++){
    float *ptr = ( float *)( srcMat->data.ptr + row * srcMat->step );
    for( int col = 0; col < srcMat->cols; col++){
        // +0 -> B; +1 -> G; +2 -> R
        ptr[ 3*col ] = 255;
        ptr[ 3*col + 1 ] = 0;
        ptr[ 3*col + 2 ] = 0;
    }
    }
```
**特别注意：** 根据联合体`data`可知，访问数据的指针类型（而不是指针指向的数据的类型） 可以有多种选择。需要注意的是，当指针类型不同时，我们在将某点指针指向该点的下一行元素时，需要对`step`进行相关的算术运算处理才能得到正确的偏移量。具体如下： 
- 指针类型为`uchar（data.ptr）`时,
    float *ptr = ( float *)( srcMat->data.ptr + row * srcMat->step );

- 指针类型为`short（data.s）`时,
    float *ptr = ( float *)( srcMat->data.s + row * srcMat->step/2 );
- 指针类型为`int/float（data.i/data.fl）`时,
    float *ptr = ( float *)( srcMat->data.i /*srcMat->data.fl*/ + row * srcMat->step/4 );
- 指针类型为`double（data.db）`时,
    float *ptr = ( float *)( srcMat->data.db + row * srcMat->step/8 );
3. 由程序可知，我们为每行都重新计算了 `ptr` ，而不是简单地从开头开始，尔后每次读的时候累加指针。

# IplImage数据结构
## IplImage结构
```c++
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

## 访问图像数据
通常，我们需要非常迅速和高效地访问图像中的数据。这意味着我们不应受制于存取函数（如`cvSet*D`之类）。实际上，我们想要用最直接的方式访问图像内的数据。现在，应用已掌握的 `IplImage` 内部结构的知识，我们知道怎样做才是最佳方法。 
**例4：**
```c++
// 作者：GongchuangSu ( http://gongchuangsu.com/ )
// 时间：2015.07.21
// 说明：通过指针遍历图像各通道，并进行相关设置改变图像色彩值

#include <cv.h>
#include <cxcore.h>
#include <highgui.h>
#include <iostream>
using namespace std;

int main(int argc, char * argv[]){
    const char *ImageName="cat.jpg";
    const char *WindowsTitle="Cat";
    IplImage *srcImg=cvLoadImage(ImageName);
    // 显示图像的相关属性
    cout << "srcImg->width = " << srcImg->width << endl;
    cout << "srcImg->height = " << srcImg->height << endl;
    cout << "srcImg->imageSize = " << srcImg->imageSize << endl;
    cout << "srcImg->nChannels = " << srcImg->nChannels << endl;
    cout << "srcImg->depth = " << srcImg->depth << endl;
    cout << "srcImg->widthStep = " << srcImg->widthStep << endl;
    // 循环遍历、设置
    for( int row = 0; row < srcImg->height; row++){
        uchar *ptr = (uchar *)(
            srcImg->imageData + row * srcImg->widthStep
        );
        for( int col = 0; col < srcImg->width; col++){
            // 设置三通道B、G、R色彩值
            ptr[3 * col] = 150;
            //ptr[3 * col + 1] = 120;
            //ptr[3 * col + 2] = 120;
        }
    }
    // 创建窗口
    cvNamedWindow(WindowsTitle,0);
    // 显示图片
    cvShowImage(WindowsTitle,srcImg);
    // 使程序暂停，等待触发按键操作
    cvWaitKey(0);
    // 释放内存
    cvReleaseImage(&srcImg);
    // 销毁窗口
    cvDestroyWindow(WindowsTitle);
    return(0);
}
```
**运行结果： **
![](http://i.imgur.com/JExoZhD.jpg)
![](http://i.imgur.com/0stnUv4.jpg)

**说明：**
该例程中，我们使用指针遍历图像，类似于例3中的矩阵遍历。然而，有一些细微的不同，是源于 `IplImage` 和 `CvMat` 结构的差异。与 `CvMat` 的成员 `data` 相比，`IplImage` 和 `CvMat` 之间的一个重要的区别在于 `imagedata` 。`CvMat` 的 `data` 元素类型是联合类型，所以必须说明需要使用的指针类型。`imagedata` 指针是字节类型指针`（uchar *）`，这意味着，在图像进行指针运算时，我们可以简单的增加 `widthstep` （也是以字节为单位），而不必关心实际数据类型。当要处理的是矩阵时，必须对偏移进行调整，因为数据指针可能是非字节类型；当要处理的是图像时，可以直接使用偏移，因为数据指针总是字节类型。

`ROI` 和 `widthstep` 在实际工作中有很重要的作用，在很多情况下，使用它们会提高计算机视觉代码的执行速度。这是因为它们允许对图像的某一小部分进行操作，而不是对整个图像进行运算。 
为了解释 `ROI` 的用法，我们编写程序加载一幅图像并修改一些区域，读取一幅图像，并设置想要的 `ROI` 的 `x, y, width `和 `height` 的值，最后将 `ROI` 区域中的像素都加上一个整数。

**例5：**
```c++
// 作者：GongchuangSu ( http://gongchuangsu.com/ )
// 时间：2015.07.19
// 说明：读取一幅图像，并设置特定的ROI的x,y,width和height的值，最后将ROI区
//       域中像素都加上一个整数。该例程实现在猫脸上用ROI增加150像素。

#include <cv.h>
#include <highgui.h>
#pragma comment(linker, "/subsystem:\"windows\" /entry:\"mainCRTStartup\"")
int main(int argc, char ** argv){
    const char *ImageName="cat.jpg";
    const char *WindowsTitle="ROI_ADD";
    IplImage *src = cvLoadImage(ImageName,1);
    int x = 230;
    int y = 50;
    int width = 250;
    int height = 250;
    int add = 150;
    // 设置图像ROI
    cvSetImageROI(src, cvRect(x,y,width,height));
    // 进行或运算，增加图像ROI的像素
    cvAddS(src, cvScalar(add,0,0), src);
    // 释放图像ROI
    cvResetImageROI(src);
    cvNamedWindow(WindowsTitle, 0);
    cvShowImage(WindowsTitle, src);
    // 等待按键触发
    cvWaitKey(0);
    // 释放内存，销毁窗口
    cvReleaseImage(&src);
    cvDestroyWindow(WindowsTitle);
    return 0;
}
```

**运行结果：**
![](http://i.imgur.com/c5IPaE9.jpg)
通过巧妙地使用`widthstep`，我们可以达到同样的效果。要做到这一点，我们首先创建另一个图像头，让它的 `width` 和 `height` 的值等于 `interest_rect` 的 `width` 和 `height` 的值。我们还需要按照 `interest_rect` 起点设置图像起点（左上角或左下角）。下一步，我们设置子图像的 `widthstep` 与较大的 `interest_img` 相同。这样，即可在子图像中逐步地进到大图像里的子区域中下一行开始处的合适位置。最后设置子图像的 `imagedate` 指针指向兴趣子区域的开始。

**例6：**
```c++
// 作者：GongchuangSu ( http://gongchuangsu.com/ )
// 时间：2015.07.21
// 说明：利用widthstep方法把图像感兴趣区域的所有像素值增加一定数值。

#include <cv.h>
#include <highgui.h>
#pragma comment(linker, "/subsystem:\"windows\" /entry:\"mainCRTStartup\"")
int main(int argc, char ** argv){
    const char *ImageName="cat.jpg";
    const char *WindowsTitle="ROI_ADD";
    // 设置感兴趣区域的起始点坐标和该区域的大小
    int x = 230;
    int y = 50;
    int width = 250;
    int height = 250;
    int add = 150;
    // 创建感兴趣区域图像头
    IplImage *srcImage = cvLoadImage(ImageName,1);
    IplImage *subImage = cvCreateImageHeader(
        cvSize( width, height),
        srcImage->depth,
        srcImage->nChannels
    );
    subImage->origin = srcImage->origin;
    subImage->widthStep = srcImage->widthStep;
    subImage->imageData = srcImage->imageData +
        ( y - 1 ) * srcImage->widthStep +
        ( x - 1 ) * srcImage->nChannels;
    // 进行或运算，增加图像ROI的像素
    cvAddS(subImage, cvScalar(add,0,0), subImage);
    cvNamedWindow(WindowsTitle, 0);
    cvShowImage(WindowsTitle, subImage);
    // 等待按键触发
    cvWaitKey(0);
    // 释放内存，销毁窗口
    cvReleaseImage(&srcImage);
    cvReleaseImageHeader(&subImage);
    cvDestroyWindow(WindowsTitle);
    return 0;
}
```

**运行结果： **
![](http://i.imgur.com/q1hyoNQ.jpg)
看起来设置和重置 ROI 更方便一些，为什么还要使用 **widthstep** ？ 原因在于有些时候在处理的过程中，想在操作过程中设置和保持一幅图像的多个子区域处于活动状态，但是 ROI 只能串行处理并且必须不断地设置和重置。
除了上面两种方法来设置图像部分区域的像素值外，还可以通过底层指针遍历特定的部分区域。

**例7：**
```c++
// 作者：GongchuangSu ( http://gongchuangsu.com/ )
// 时间：2015.07.21
// 说明：通过指针遍历图像各通道，并进行相关设置改变图像色彩值,实现
//       与函数cvAddS（）同样的功能。

#include <cv.h>
#include <cxcore.h>
#include <highgui.h>
#include <iostream>
using namespace std;

int main(int argc, char * argv[]){
    const char *ImageName="cat.jpg";
    const char *WindowsTitle="ROI_ADD";
    IplImage *srcImg=cvLoadImage(ImageName);
    // 显示图像的相关属性
    cout << "srcImg->width = " << srcImg->width << endl;
    cout << "srcImg->height = " << srcImg->height << endl;
    cout << "srcImg->imageSize = " << srcImg->imageSize << endl;
    cout << "srcImg->nChannels = " << srcImg->nChannels << endl;
    cout << "srcImg->depth = " << srcImg->depth << endl;
    cout << "srcImg->widthStep = " << srcImg->widthStep << endl;
    // 设置感兴趣区域的起始点坐标和该区域的大小
    int x = 230;
    int y = 50;
    int width = 250;
    int height = 250;
    int add = 150;
    // 循环遍历、设置
    for( int row = y; row < ( y + height ); row++){
        uchar *ptr = (uchar *)(
            srcImg->imageData + row * srcImg->widthStep
        );
        for( int col = x; col < ( x + width ); col++){
            // 设置三通道B、G、R色彩值
            // B
            if( ptr[3 * col] + add >= 255 )
                ptr[3 * col] = 255;
            else
                ptr[3 * col] += add;
            //// G
            //if( ptr[3 * col + 1] + add >= 255 )
            //  ptr[3 * col + 1] = 255;
            //else
            //  ptr[3 * col + 1] += add;
            //// R
            //if( ptr[3 * col + 2] + add >= 255 )
            //  ptr[3 * col + 2] = 255;
            //else
            //  ptr[3 * col + 2] += add;
        }
    }
    // 创建窗口
    cvNamedWindow(WindowsTitle,0);
    // 显示图片
    cvShowImage(WindowsTitle,srcImg);
    // 使程序暂停，等待触发按键操作
    cvWaitKey(0);
    // 释放内存
    cvReleaseImage(&srcImg);
    // 销毁窗口
    cvDestroyWindow(WindowsTitle);
    return(0);
}
```

**运行结果：**
![](http://i.imgur.com/YCWIXQ7.jpg)