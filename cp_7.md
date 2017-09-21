# 第七章 让我们把面板显示出来吧

[>>点此回到教程目录](pro_doc.md)

我们的角色老是在左上角处于被切了一半的状态，看起来很没意思，这一节就让我们把最低程度的框架给搭好吧。

为了保存面板（UI）的图片,首先在GV.h里面先定义变量。

–在GV.h中如下追加-
```cpp
GLOBAL int img_board[40];
```	
为下一节做准备，我们顺便先准备一个初始化函数吧。

接下来，我们定义表示出角色可以自由运动的领域的范围。

FIELD_MAX_X,　FILED_MAX_Y是各种角色可以自由移动的领域的最大尺寸。

FIELD_X, FILED_Y是这一领域的左上角坐标。

下述的数值描述了这样一个角色可运动的区域：从（32,16）坐标开始，横向384纵向448像素的区域。

–define.h变更–
```cpp
#include "struct.h"
 
#define FIELD_MAX_X 384
#define FIELD_MAX_Y 448
 
#define FIELD_X 32
#define FIELD_Y 16
```
我们使用calc_ch来计算显示角色的图像。

通过递增计数器，以24次递增为一个周期。在此期间有四种类型的图像轮流显示。

如果您看了数据文件中的角色图片的话，您就会明白了，角色是四种图片一个动作，因此我们使用下面的式子来循环计算图片的编号。

–char.cpp变更–
```cpp
#include "../include/GV.h"
 
void calc_ch(){
        ch.cnt++;
        ch.img=(ch.cnt%24)/6;
}
```
声明在主循环中调用的函数的时候，也要写在function.h中。

–向function.h进行以下追加-
```cpp
GLOBAL void calc_ch();
```
在load.cpp中读入面板的图像数据。

至于为何图片用于编号的数字并不是连续的，这并没有什么特别的意义，不过这样编号的话后面的更换图片会方便一点。其实从0开始连续编号也完全没有问题。

–load.cpp变更-
```cpp
#include "../include/GV.h"
 
void load(){
        img_board[10] = LoadGraph("../dat/img/board/10.png");
        img_board[11] = LoadGraph("../dat/img/board/11.png");
        img_board[12] = LoadGraph("../dat/img/board/12.png");
        img_board[20] = LoadGraph("../dat/img/board/20.png");
        LoadDivGraph( "../dat/img/char/0.png" , 12 , 4 , 3 , 73 , 73 , img_ch[0] ) ;
}
```
设定角色的初始坐标。

–ini.cpp变更–
```cpp
#include "../include/GV.h"
 
//最开始的初始化
void first_ini(){
        ch.x=FIELD_MAX_X/2;
        ch.y=FIELD_MAX_Y*3/4;
}
```

为了描绘面板我们使用graph_board函数。

–graph.cpp变更–
```cpp
#include "../include/GV.h"
 
void graph_ch(){
        DrawRotaGraphF(ch.x+FIELD_X,ch.y+FIELD_Y,1.0f,0.0f,img_ch[0][ch.img],TRUE);
}
 
void graph_board(){
        DrawGraph(  0,  0,img_board[10],FALSE);
        DrawGraph(  0, 16,img_board[11],FALSE);
        DrawGraph(  0,464,img_board[12],FALSE);
        DrawGraph(416,  0,img_board[20],FALSE);
}
 
void graph_main(){
        graph_ch();
        graph_board();
}
```
然后仅是在main.cpp的主循环中的case 0中加入“first_ini();”。

–main.cpp变更–
```cpp
#define GLOBAL_INSTANCE 
#include "../include/GV.h"
 
//循环必须处理的三大过程
int ProcessLoop(){
    if(ProcessMessage()!=0)return -1; //过程处理如果发生错误返回-1
    if(ClearDrawScreen()!=0)return -1; //如果画面清空错误返回-1
    GetHitKeyStateAll_2();//进行当前按键处理
    return 0;
}
 
int WINAPI WinMain(HINSTANCE hInstance,HINSTANCE hPrevInstance,LPSTR lpCmdLine,int nCmdShow){
    ChangeWindowMode(TRUE);//窗口模式
    if(DxLib_Init() == -1 || SetDrawScreen( DX_SCREEN_BACK )!=0) return -1;//初始化和里画面化
 
    while(ProcessLoop()==0){//主循环
                switch(func_state){
                        case 0:
                                load();         //载入数据
                                first_ini();//最开始的初始化
                                func_state=100;
                                break;
                        case 100:
                                calc_ch();
                                graph_main();//绘制main
                                break;
                        default:
                                printfDx("错误的func_state\n");
                                break;
                }
 
                if(CheckStateKey(KEY_INPUT_ESCAPE)==1)break;//按下ESC键则跳出循环
    ScreenFlip();//里外画面翻转
    }
 
    DxLib_End();//DX Library终结处理
    return 0;
}
```
---

运行结果

![](http://dixq.net/rp/img/7/0.JPG)

---

[>>点此回到教程目录](pro_doc.md)