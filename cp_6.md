# 第六章 控制一下在主循环中调用的函数吧

[>>点此回到教程目录](pro_doc.md)

在主循环中调用的函数，会根据某个时刻的不同状况产生不同的回馈，没错吧。 也就是说，我们必须要在主循环中控制我们调用的函数。 因此，我们事先准备好一个叫func_state的变量，向其带入表示状态的值，以此为依据来进行条件分歧吧。 由于我们希望将func_state变量用作全局变量，首先向GV.h中如下追加：

–向GV.h进行如下追加：-
```cpp
GLOBAL int func_state;
```	
我们顺便为下一章节做个准备，为其先准备一个初始化函数吧。

–在ini.cpp中进行以下追加–
```cpp
void first_ini(){
 
}
```
声明要在主循环中调用的函数的时候，需要在function.h里面写上。

–在function.h中进行如下追加–
```cpp
GLOBAL void first_ini();
```	
在主循环中以准备好了的func_state为一句，我们使用switch语句进行条件分歧判断。

–main.cpp変更–
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
                                graph_main();//绘制main
                                break;
                        default:
                                printfDx("错误的func_state\n");
                                break;
                }
 
                if(CheckStateKey(KEY_INPUT_ESCAPE)==1)break;//如果按下ESC键则跳出循环
        ScreenFlip();//里外画面翻转
    }
 
    DxLib_End();//DX Library终结处理
    return 0;
}
```
从第四节开始我们的运行结果就没有改变。和以前一样执行的话同样会成功。

[>>点此回到教程目录](pro_doc.md)