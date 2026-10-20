# OpenCV C++ 算法总结

> 这里汇总了 **# 4h上手C++版Opencv** 中 8 个 chapter 源码（chapter 1 ~ chapter 8）所使用的全部 OpenCV 算法与函数。

---

## 0. 公共头文件与命名空间

```cpp
#include <opencv2/imgcodecs.hpp>   //图像编解码：imread、imwrite
#include <opencv2/highgui.hpp>     //高层界面：imshow、waitKey、namedWindow、createTrackbar
#include <opencv2/imgproc.hpp>     //图像处理：cvtColor、滤波、Canny、形态学、轮廓、绘图
#include <opencv2/objdetect.hpp>   //目标检测：CascadeClassifier（人脸识别）
#include <iostream>

using namespace cv;    //OpenCV 命名空间
using namespace std;   //标准库命名空间
```

| 头文件 | 负责的算法模块 |
|:---|:---|
| `imgcodecs.hpp` | 图像的读（`imread`）与写（`imwrite`） |
| `highgui.hpp` | 窗口显示与交互（`imshow`、`waitKey`、滑杆 `createTrackbar`） |
| `imgproc.hpp` | 色彩空间、滤波、边缘、形态学、几何变换、轮廓、绘图 |
| `objdetect.hpp` | 级联分类器目标检测（人脸、车牌） |

---

## 1. Chapter 1 —— 读取图像 / 视频 / 摄像头

三种输入源共用同一套「读取 → 显示 → 等待」循环结构，区别只在于数据来源。

### 1.1 读取并显示图像

```cpp
string path = "Resources/test.png";  //路径字符串
Mat img = imread(path);              //通过路径读取图像
imshow("Image", img);                //显示，窗口名，图像
waitKey(0);                          //等待按键，0 表示无限等待
```

| 函数 | 参数说明 |
|:---|:---|
| `imread(path)` | 图像路径字符串，返回 `Mat` |
| `imshow(winname, mat)` | 窗口名，要显示的图像 |
| `waitKey(delay)` | 延时毫秒数；`0` 表示无限等待按键 |

### 1.2 读取视频文件

```cpp
VideoCapture cap(path);  //打开视频源：文件路径 / 摄像头索引
Mat img;

while (true) {
    cap.read(img);       //读取下一帧，输出图像
    imshow("Image", img);
    waitKey(20);         //等待 20 ms，约等于 50 FPS
}
```

### 1.3 读取摄像头（Webcam）

```cpp
VideoCapture cap(0);  //0 表示默认摄像头
```

> **关键点**
> - `VideoCapture` 的参数既可以是**文件路径字符串**，也可以是**摄像头索引整数**，两者接口统一。
> - 视频/摄像头循环中 `waitKey` 的数值直接决定播放帧率与刷新速度。
> - 图像用 `waitKey(0)`（静态等按键），视频用 `waitKey(1~20)`（连续刷新）。

---

## 2. Chapter 2 —— 基础图像处理


```cpp
string path = "Resources/test.png";
Mat img = imread(path);
Mat imgGray;

cvtColor(img, imgGray, COLOR_BGR2GRAY);              //将图像转换（二值化、hsv，看着很猎奇是因为把hsv的数值用bgr方式输出了
GaussianBlur(img, imgBlur, Size(11, 11), 0, 0);      //高斯模糊，输入输出，核大小，X方向标准差，Y方向标准差
Canny(imgBlur, imgcanny, 25, 75);                    //边缘检测，输入输出，低阈值，高阈值

Mat kernel = getStructuringElement(MORPH_RECT, Size(5, 5));  //获取形态学核，形状，大小

dilate(imgcanny, imgdia, kernel);   //膨胀，输入输出，核
erode(imgdia, imgerode, kernel);    //腐蚀，输入输出，核

imshow("Image", imgcanny);
waitKey(0);
```

| 函数                                              | 参数说明                                               | 作用                 |
| :---------------------------------------------- | :------------------------------------------------- | :----------------- |
| `cvtColor(src, dst, code)`                      | 输入，输出，色彩空间转换码                                      | BGR↔GRAY、BGR↔HSV 等 |
| `GaussianBlur(src, dst, ksize, sigmaX, sigmaY)` | 输入，输出，核大小，X 标准差，Y 标准差                              | 平滑去噪，核必须为奇数        |
| `Canny(image, edges, thr1, thr2)`               | 输入，输出，低阈值，高阈值                                      | 双阈值边缘检测            |
| `getStructuringElement(shape, ksize)`           | 形状（`MORPH_RECT`/`MORPH_CROSS`/`MORPH_ELLIPSE`），核大小 | 生成形态学结构元素          |
| `dilate(src, dst, kernel)`                      | 输入，输出，核                                            | 膨胀：加粗白色前景，连接断裂     |
| `erode(src, dst, kernel)`                       | 输入，输出，核                                            | 腐蚀：细化白色前景，去掉毛刺     |

> **关键点**
> - 顺序一般为：**灰度 → 模糊 → 边缘 → 形态学**；先模糊再 Canny 可显著减少噪点边缘。
> - `getStructuringElement` 的核越大，膨胀/腐蚀效果越强，`Size(3,3)` 是常用起点。
> - 膨胀与腐蚀互为对偶操作，先膨胀再腐蚀即为「闭运算」，用于填补内部空洞。

---

## 3. Chapter 3 —— 缩放与裁剪

```cpp
Mat img = imread(path);
Mat imgResize, imgcrop;

resize(img, imgResize, Size(), 0.5, 0.5);  //缩放，输入输出，目标尺寸，X方向比例，Y方向比例

Rect roi(200, 100, 300, 300);  //矩形区域：左上角 x、左上角 y、宽、高
imgcrop = img(roi);            //用矩形切片取出裁剪区域

imshow("Image", imgcrop);
waitKey(0);
```

| 函数 | 参数说明 |
|:---|:---|
| `resize(src, dst, dsize, fx, fy)` | 输入，输出，目标尺寸（填 `Size()` 时用比例），X 缩放比例，Y 缩放比例 |
| `Rect(x, y, width, height)` | 左上角 x、左上角 y、宽、高 |
| `img(roi)` | 以矩形作为 ROI 提取子图，与源图**共享内存** |

> **关键点**
> - `dsize` 与 `fx/fy` 二选一：给了 `fx/fy` 就把 `dsize` 写成空 `Size()`。
> - `img(roi)` 得到的是**视图**而非拷贝，若要独立图像需加 `.clone()`。
> - `Rect` 的四个参数顺序容易记反，注意是 **(x, y, 宽, 高)** 而不是两个顶点。

---

## 4. Chapter 4 —— 绘制形状与文字

```cpp
Mat img(512, 512, CV_8UC3, Scalar(255, 255, 255));  // 创造 大小、8位3图层、何色 图片

circle(img, Point(256, 256), 155, Scalar(0, 69, 255), -1);  // 找中心画圆

Rect rec(100, 100, 400, 200);  //左上角，长宽
rectangle(img, Point(130, 226), Point(382, 286), Scalar(255, 255, 255), -1);  // 用两个顶点绘制矩形
line(img, Point(130, 296), Point(382, 296), Scalar(255, 255, 255), 2);        //不可填充，画直线
putText(img, "ataka", Point(137, 262), FONT_HERSHEY_COMPLEX, 0.75, Scalar(0, 89, 255), 1);  //定义开始点，字体，比例，颜色，线宽
```

| 函数 | 参数说明 |
|:---|:---|
| `Mat(rows, cols, type, color)` | 行数，列数，类型（`CV_8UC3` = 8 位 3 通道），填充颜色 |
| `circle(img, center, radius, color, thickness)` | 画布，圆心，半径，颜色，线宽（`-1` 为实心填充） |
| `rectangle(img, pt1, pt2, color, thickness)` | 画布，顶点 1，顶点 2，颜色，线宽 |
| `rectangle(img, rect, color, thickness)` | 画布，`Rect` 矩形对象，颜色，线宽 |
| `line(img, pt1, pt2, color, thickness)` | 画布，起点，终点，颜色，线宽 |
| `putText(img, text, org, fontFace, fontScale, color, thickness)` | 画布，文字，起始点，字体，缩放比例，颜色，线宽 |
| `Scalar(b, g, r)` | 颜色按 **BGR** 顺序排列，与直觉的 RGB 相反 |

> **关键点**
> - `Scalar` 是 BGR 顺序。
> - 线宽参数传 `-1`（或 `FILLED`）表示实心填充；`line` 不可填充。
> - `CV_8UC3` 中 `8U` = 8 位无符号，`C3` = 3 通道；灰度图则用 `CV_8UC1`。
> - 绘图函数默认**原地修改**传入的图像，无需接收返回值。

---

## 5. Chapter 5 —— 透视变换（鸟瞰图）

从倾斜拍摄的卡片照片中，把卡片「拉正」成正视图。

```cpp
float w = 250, h = 350;
Mat matrix, imgwarp;

Point2f src[4] = { {741,380},{1026,435},{644,709},{967,784} };  //源图上的四个角点
Point2f dst[4] = { {0.0f,0.0f},{w,0.0f},{0.0f,h},{w,h} };       //目标图上的四个角点

matrix = getPerspectiveTransform(src, dst);            //计算透视变换矩阵，源四点，目标四点
warpPerspective(img, imgwarp, matrix, Point(w, h));    //应用透视变换，输入输出，变换矩阵，输出尺寸

for (int i = 0; i < 4; i++) {
    circle(img, src[i], 10, Scalar(0, 69, 255), -1);   // 标出四个源角点便于调试
}
```

| 函数 | 参数说明 |
|:---|:---|
| `getPerspectiveTransform(src, dst)` | 源四点数组，目标四点数组，返回 3×3 变换矩阵 |
| `warpPerspective(src, dst, M, dsize)` | 输入，输出，变换矩阵，输出图像尺寸 |
| `Point2f(x, y)` | 浮点坐标点，透视变换必须用 `float` 类型 |

> **关键点**
> - `src` 与 `dst` 必须是**一一对应**的四个点，且顺序一致，四点顺序错乱会导致图像翻转或扭曲。
> - 源角点坐标通常用取色/鼠标回调人工标定，调试时用 `circle` 画出来验证。
> - 与之相对的是仿射变换 `getAffineTransform` + `warpAffine`，只需要 3 个点，但只能做平行四边形映射。

---

## 6. Chapter 6 —— HSV 颜色检测

```cpp
Mat imghsv, mask;
int hmin = 0, smin = 110, vmin = 153;
int hmax = 19, smax = 240, vmax = 255;

Mat img = imread(path);
cvtColor(img, imghsv, COLOR_BGR2HSV);  //将图像转换（二值化、hsv，看着很猎奇是因为把hsv的数值用bgr方式输出了

namedWindow("trackbars", 0);                        //定义一个窗口，前面是名字，后面是0：自由拉伸，1：自适应固定大小
createTrackbar("Hue min", "trackbars", &hmin, 179); //创造一个滑杆，名字、所处窗口
createTrackbar("Hue max", "trackbars", &hmax, 179);
createTrackbar("Sat min", "trackbars", &smin, 255);
createTrackbar("Sat max", "trackbars", &smax, 255);
createTrackbar("Val min", "trackbars", &vmin, 255);
createTrackbar("Val max", "trackbars", &vmax, 255);

while (true) {
    Scalar lower(hmin, smin, vmin), upper(hmax, smax, vmax);  //HSV 下限与上限

    inRange(imghsv, lower, upper, mask);  //按范围取掩膜，输入，下限，上限，输出

    imshow("img", img);
    imshow("Image", mask);
    waitKey(1);
}
```

| 函数 | 参数说明 |
|:---|:---|
| `createTrackbar(name, winname, value, count)` | 滑杆名，所在窗口名，绑定的变量指针，最大值 |
| `namedWindow(name, flags)` | 窗口名，`0` = 自由拉伸，`1` = 自适应固定大小 |
| `inRange(src, lowerb, upperb, dst)` | 输入，下限，上限，输出二值掩膜 |
| `Scalar(h, s, v)` | 此处承载 HSV 三通道阈值 |

> **关键点**
> - OpenCV 中 HSV 的取值范围是 **H: 0~179，S: 0~255，V: 0~255**（不是常见的 0~360）。
> - `inRange` 输出为**单通道二值图**：范围内为 255（白），范围外为 0（黑）。
> - `createTrackbar` 绑定的是**变量地址** `&hmin`，滑杆拖动时变量实时变化，因此必须在循环内重新计算。
> - HSV 比 BGR 更适合做颜色分割，因为色调 H 与光照强度 V 相对解耦。
> - 掩膜常见后续用法：`bitwise_and(img, img, result, mask)` 抠出彩色目标。

---

## 7. Chapter 7 —— 轮廓与形状检测

### 7.1 主流程

```cpp
cvtColor(img, imgGray, COLOR_BGR2GRAY);
GaussianBlur(imgGray, imgBlur, Size(11, 11), 0, 0);
Canny(imgBlur, imgcanny, 25, 75);

Mat kernel = getStructuringElement(MORPH_RECT, Size(3, 3));
dilate(imgcanny, imgdil, kernel);   //膨胀让轮廓更连续，便于 findContours

getcontours(imgdil, img);
```

### 7.2 轮廓提取与形状分类

```cpp
void getcontours(Mat imgdil, Mat img) {
    vector<vector<Point>> contours;  //收集具体轮廓
    vector<Vec4i> hierarchy;         //收集轮廓关系
    string objectType;               //存储轮廓名字

    findContours(imgdil, contours, hierarchy, RETR_EXTERNAL, CHAIN_APPROX_SIMPLE);  //取图形轮廓，存入轮廓数组，关系保留程度，轮廓保留程度

    for (int i = 0; i < contours.size(); i++) {
        int area = contourArea(contours[i]);  //contourArea 用来得到对应轮廓的面积

        vector<vector<Point>> conpoly(contours.size());  //精简轮廓
        vector<Rect> boundRect(contours.size());         //正立正方形轮廓

        if (area > 1000) {                                          //面积过小的是噪点，过滤掉
            float peri = arcLength(contours[i], true);              //计算轮廓周长，给出是否闭合
            approxPolyDP(contours[i], conpoly[i], 0.02*peri, true); //用更少的点表示轮廓，输入输出，精度，是否闭合
            boundRect[i] = boundingRect(conpoly[i]);                //用正立 正方形 来框柱 轮廓

            int objCor = (int)conpoly[i].size();  //精简轮廓点数
            if (objCor == 3) { objectType = "tri"; }
            if (objCor == 4) {
                float aspRatio = (float)boundRect[i].width / (float)boundRect[i].height;
                if (aspRatio > 0.9 && aspRatio < 1.1) { objectType = "square"; }
                else objectType = "rect";
            }
            if (objCor > 4) { objectType = "circle"; }

            drawContours(img, conpoly, i, Scalar(255, 0, 255), 3);  // 画布，轮廓集，画第几个（-1全画），颜色，粗
            rectangle(img, boundRect[i].tl(), boundRect[i].br(), Scalar(0, 255, 0), 5);  // 用两个顶点绘制矩形
            putText(img, objectType, { boundRect[i].x, boundRect[i].y - 5 },
                    FONT_HERSHEY_PLAIN, 1, Scalar(0, 89, 255), 1.5);  //定义开始点，字体，比例，颜色，线宽
        }
    }
}
```

| 函数 | 参数说明 |
|:---|:---|
| `findContours(image, contours, hierarchy, mode, method)` | 输入，轮廓数组，层级关系，关系保留程度，轮廓保留程度 |
| `contourArea(contour)` | 计算单个轮廓所围面积，用于过滤噪点 |
| `arcLength(curve, closed)` | 计算轮廓周长，给出是否闭合 |
| `approxPolyDP(curve, approxCurve, epsilon, closed)` | 输入输出，精度（越小越贴合），是否闭合 |
| `boundingRect(array)` | 返回正立外接矩形 `Rect` |
| `drawContours(img, contours, idx, color, thickness)` | 画布，轮廓集，画第几个（`-1` 全画），颜色，粗细 |
| `Rect::tl()` / `Rect::br()` | 矩形左上角 / 右下角顶点 |

**`findContours` 常用模式取值**

| 参数 | 取值 | 含义 |
|:---|:---|:---|
| 关系保留程度 `mode` | `RETR_EXTERNAL` | 只取最外层轮廓（本课使用） |
| | `RETR_LIST` | 提取所有轮廓，不建立层级 |
| | `RETR_TREE` | 建立完整层级树 |
| 轮廓保留程度 `method` | `CHAIN_APPROX_SIMPLE` | 只保留端点，压缩水平/垂直/斜向段 |
| | `CHAIN_APPROX_NONE` | 保留轮廓上所有点 |

> **关键点**
> - `approxPolyDP` 的精度 `epsilon` 常取 `0.02 * peri`：值越大点越少，越小越贴合原轮廓。
> - 四边形再用**宽高比 aspRatio** 区分正方形与矩形，`0.9 ~ 1.1` 视为接近 1:1。
> - `area > 1000` 这类面积阈值是降噪手段，否则会画出大量碎片轮廓。
> - 输入给 `findContours` 的图最好是**边缘或二值图**（本例是 `imgdil`）；OpenCV 4.x 不再修改传入图像。

---

## 8. Chapter 8 —— 人脸检测

基于 Viola-Jones 的 Haar 级联分类器，属于传统机器学习方法。

```cpp
CascadeClassifier faceCascade;                                      //检测器
faceCascade.load("Resources/haarcascade_frontalface_default.xml");  //导入训练文件

if (faceCascade.empty()) { cout << "xml no no no" << endl; }  //检测是否加载成功

vector<Rect> faces;
faceCascade.detectMultiScale(img, faces, 1.1, 3);  //滑动窗口找人脸，输入输出，缩小幅度，相邻检验数

for (int i = 0; i < faces.size(); i++) {
    rectangle(img, faces[i].tl(), faces[i].br(), Scalar(255, 0, 255), 3);  // 用两个顶点绘制矩形
}
```

| 函数 | 参数说明 |
|:---|:---|
| `CascadeClassifier()` | 构造一个空的级联分类器 |
| `load(filename)` | 载入训练好的 XML 模型文件 |
| `empty()` | 判断模型是否加载成功，返回 `bool` |
| `detectMultiScale(image, objects, scaleFactor, minNeighbors)` | 输入图像，输出矩形数组，每次缩放幅度，相邻检测框最小数 |

**`detectMultiScale` 参数深入**

| 参数 | 含义 | 调大 | 调小 |
|:---|:---|:---|:---|
| `scaleFactor` | 图像金字塔每次缩小的比例，常用 `1.1` | 检测更快、更易漏检 | 检测更慢、更细致 |
| `minNeighbors` | 一个候选框至少被多少个邻框确认 | 误检更少，可能漏检 | 检出更多，误检增加 |

> **关键点**
> - 输入图像必须是**灰度图**效果最好，彩色图内部也会转灰度处理，建议提前 `cvtColor`。
> - XML 模型路径为**相对工作目录**，路径写错时 `load` 不报错但 `empty()` 返回 `true`。
> - 常用级联模型：`haarcascade_frontalface_default.xml`（正脸）、`haarcascade_russian_plate_number.xml`（俄罗斯车牌）。
> - 结合 Chapter 1 的摄像头循环，即可实现**实时人脸检测**。

---

## 9. 算法速查表

| 分类 | 函数 | 一句话用途 |
|:---|:---|:---|
| 输入 | `imread` | 按路径读取图像 |
| 输入 | `VideoCapture` | 打开视频文件或摄像头 |
| 输入 | `cap.read` | 读取下一帧 |
| 输出 | `imshow` / `waitKey` | 显示图像 / 等待按键 |
| 色彩 | `cvtColor` | BGR↔GRAY / BGR↔HSV |
| 滤波 | `GaussianBlur` | 高斯平滑去噪 |
| 边缘 | `Canny` | 双阈值边缘检测 |
| 形态学 | `getStructuringElement` | 生成形态学核 |
| 形态学 | `dilate` / `erode` | 膨胀加粗 / 腐蚀细化 |
| 几何 | `resize` | 缩放图像 |
| 几何 | `Rect` + `img(roi)` | 裁剪 ROI |
| 几何 | `getPerspectiveTransform` + `warpPerspective` | 透视变换 / 摆正 |
| 阈值 | `inRange` | 按 HSV 范围生成掩膜 |
| 交互 | `namedWindow` / `createTrackbar` | 创建窗口与调节滑杆 |
| 轮廓 | `findContours` | 提取轮廓集合 |
| 轮廓 | `contourArea` / `arcLength` | 计算面积 / 周长 |
| 轮廓 | `approxPolyDP` | 多边形逼近，精简顶点 |
| 轮廓 | `boundingRect` | 获取外接矩形 |
| 绘图 | `circle` / `rectangle` / `line` / `putText` | 画圆 / 矩形 / 线 / 文字 |
| 绘图 | `drawContours` | 绘制轮廓 |
| 检测 | `CascadeClassifier` | 载入 Haar 模型并检测目标 |

---
