# 第三章 注意一下对全局变量的处理吧

[>>点此回到教程目录](pro_doc.md)

刚才我们直接将处理按键输入的GetHitKeyStateAll_2函数从样例中复制粘贴过来了。

按照前面那样，我们必须把名为Key的数组设为全局变量。

但是全局变量在任何地方都可能被修改，我们希望尽量少用全局变量。

虽然这里用了同一个全局变量，我们也要尽量尝试能减少BUG产生的方法。

–key.cpp変更–
```cpp

#include "../include/GV.h"
 
unsigned int stateKey[256];
 
int GetHitKeyStateAll_2(){
    char GetHitKeyStateAll_Key[256];
    GetHitKeyStateAll( GetHitKeyStateAll_Key );
    for(int i=0;i<256;i++){
        if(GetHitKeyStateAll_Key[i]==1) stateKey[i]++;
        else                            stateKey[i]=0;
    }
    return 0;
}
 
int CheckStateKey(unsigned char Handle){
        return stateKey[Handle];
}
```
在main文件和另外的key文件里面试着像上面那样写。

在此之前，我们要想知道ESC键的状态的话，必须要检查Key[KEY_INPUT_ESCAPE]的值。

不过，将Key改为stateKey之后，为了在判断按键时不直接比较stateKey中对应的元素，我们向CheckStateKey函数传入按键的代码，此时如果能够返回输入状态，那么就可以降低按键值被错误地重写的危险性。

现在我们向上面实现的CheckStateKey函数传入按键的代码，这样就能够检查按键的输入状态了。

此外，如果要使用其它文件中实现的变量或者函数，那么就需要extern声明。

```cpp
//处理现在的输入
extern int GetHitKeyStateAll_2();
//返回传入的按键的编号的当前状态
extern int CheckStateKey(unsigned char Handle);
```
将这个写到main.cpp中，那么就可以在main文件中使用key文件中定义的函数了。

此外，把在上一小节里编写的、主循环中每次都会执行到的一系列处理进行整理，都放到名为ProcessLoop()的函数中。

执行的工作并没有改变。那么看看改变过后的main文件。

–main.cpp変更–
```cpp
#define GLOBAL_INSTANCE 
#include "../include/GV.h"
 
//处理现在的输入
extern int GetHitKeyStateAll_2();
//返回传入的按键的编号的当前状态
extern int CheckStateKey(unsigned char Handle);
 
//循环中必须执行的三大处理
int ProcessLoop(){
    if(ProcessMessage()!=0)return -1; //过程处理如果发生错误返回-1
    if(ClearDrawScreen()!=0)return -1; //如果画面清空错误返回-1
    GetHitKeyStateAll_2();//进行当前按键处理
    return 0;
}
 
int WINAPI WinMain(HINSTANCE hInstance,HINSTANCE hPrevInstance,LPSTR lpCmdLine,int nCmdShow){
    ChangeWindowMode(TRUE); //窗口模式
    if(DxLib_Init() == -1 || SetDrawScreen( DX_SCREEN_BACK )!=0) return -1; //初始化和里表面化
 
    while(ProcessLoop()==0){//主循环
 
        if(CheckStateKey(KEY_INPUT_ESCAPE)==1)break; //如果按下ESC键跳出循环
        ScreenFlip();//里外画面翻转
    }
 
    DxLib_End();//DX Libaray终止处理
    return 0;
}
```
按下Esc键就结束程序运行。

[>>点此回到教程目录](pro_doc.md)