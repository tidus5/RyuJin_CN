# 第四章 试试描绘角色吧

[>>点此回到教程目录](pro_doc.md)

接下来我们来试着描绘一下角色吧。

要做到这一点，首先我们要定义角色数据的结构体，同时定义它的变量。

角色数据的结构体定义如下：

–struct.h变更–
```cpp
//和角色相关的结构体
typedef struct{
    int flag;       //flag
    int cnt;        //计数变量
    int power;      //power
    int point;      //点数
    int score;      //分数
    int num;        //残机数
    int mutekicnt;  //无敌状态与计数变量
    int shot_mode;  //射击模式
    int money;      //金钱
    int img;        //图像
    int slow;       //是否缓慢移动
    double x,y;     //坐标
}ch_t;
```
结构体的的定义请写在struct.h文件中。

虽然一来就好像有很多成员，不过您并不需要把它们都记住。

一个角色会拥有各种各样的数据。比如power啊得分啊当前的射击模式啊之类的。

现在我们只是暂且把这些将来需要的东西都准备好。而目前所需要的也仅仅只是坐标罢了，所以我们也就把它放进结构体了。

换句话说目前需要注意的，也就是“结构体中有double类型的x、y成员”这一点而已。

接下来，让我们读入角色的图像。我们把图片的句柄（handle）放到img_ch变量中。

我们把12张角色图像看成一组。在龙神录中，为了实现变身的效果，需要两组这样的图像，因此我们定义变量
```cpp
int img_ch[2][12];
```
我们在main文件中定义变量、然后在下面的load.cpp中对其进行extern声明。

–load.cpp変更–
```cpp
#include "../include/GV.h"
 
extern int img_ch[2][12];
 
void load(){
    LoadDivGraph( "../dat/img/char/0.png" , 12 , 4 , 3 , 73 , 73 , img_ch[0] ) ;
}
```
请确认一下在上述过程中读入的图像。上面那个函数中的读取表达了这样的意思：将总共12幅、横4竖3、每幅都长宽73-73像素的图像放入img_ch[0]中。

如果不明白的话，请在DX Libaray的参考页面中确认这个函数。

那么我们修改一下用于执行描绘处理的graph.cpp文件。

和之前一样，在main文件中进行变量的定义，而在这里通过extern声明对其进行使用。

从现在开始，和描绘相关的函数将会越来越多，因此为了能够从main文件中很好地进行整体性的控制，我们调用graph_main函数。

–graph.cpp变更–
```cpp
#include "../include/GV.h"
 
extern ch_t ch;
extern int img_ch[2][12];
 
void graph_ch(){
    DrawRotaGraphF(ch.x,ch.y,1.0f,0.0f,img_ch[0][ch.img],TRUE);
}
 
void graph_main(){
    graph_ch();
}
```
接下来是重要的main文件，main.cpp。

–main.cpp変更–
```cpp
#define GLOBAL_INSTANCE 
#include "../include/GV.h"
 
//处理现在的输入
extern int GetHitKeyStateAll_2();
//返回传入的按键的编号的当前状态
extern int CheckStateKey(unsigned char Handle);
 
//载入数据
extern void load();
 
//绘画用的main
extern void graph_main();
 
int img_ch[2][12];      //角色图像12张　X2(变身用)
ch_t ch;                        //角色数据
 
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
 
    load();//载入数据
 
    while(ProcessLoop()==0){//主循环
 
        graph_main();//绘制main
 
        if(CheckStateKey(KEY_INPUT_ESCAPE)==1)break;//按下ESC键就跳出循环
        ScreenFlip();//里外画面翻转
    }
 
    DxLib_End();//DX Library终止处理
    return 0;
}
```
---
运行结果：

![](http://dixq.net/rp/img/4/0.PNG)

---
如果您的效果和上面一样的话那么本节的内容便完成了。您应该可以在左上角稍微看到角色。 啊，为什么main文件一下子看不懂了呢…… 没关系，我会在下一章里面给大家清晰地整理一下的。

[>>点此回到教程目录](pro_doc.md)