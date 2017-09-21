# 第二章 首先我们来执行一下吧

[>>点此回到教程目录](pro_doc.md)

首先我们来执行一下吧。向工程的main.cpp文件里添加在游戏编程馆介绍过的“程序基本骨架”，在这里贴上[链接](http://dixq.net/g/#41)。

然后编译、执行一下吧。

–main.cpp变更–

```cpp
#define GLOBAL_INSTANCE 
#include "../include/GV.h"
 
int Key[256];
 
int GetHitKeyStateAll_2(int GetHitKeyStateAll_InputKey[]){
    char GetHitKeyStateAll_Key[256];
    GetHitKeyStateAll( GetHitKeyStateAll_Key );
    for(int i=0;i<256;i++){
        if(GetHitKeyStateAll_Key[i]==1) GetHitKeyStateAll_InputKey[i]++;
        else                            GetHitKeyStateAll_InputKey[i]=0;
    }
    return 0;
}
 
int WINAPI WinMain( HINSTANCE hInstance, HINSTANCE hPrevInstance,LPSTR lpCmdLine, int nCmdShow ){
    ChangeWindowMode(TRUE);//窗口模式
    if(DxLib_Init() == -1 || SetDrawScreen( DX_SCREEN_BACK )!=0) return -1;//初始化和里画面化
 
    while(ProcessMessage()==0/*消息处理*
     && ClearDrawScreen()==0/*清空画面*/
     && GetHitKeyStateAll_2(Key)==0 /*保存输入状态*/
     && Key[KEY_INPUT_ESCAPE]==0/*没有按下ESC键*/){
        //这里！！
        ScreenFlip();
    }
 
    DxLib_End();
    return 0;
}
```
接下来，窗口就出现了，按下Esc就结束程序。

到此为止，如果您已经成功完成了，那么说明您的准备已经充足了。

[>>点此回到教程目录](pro_doc.md)