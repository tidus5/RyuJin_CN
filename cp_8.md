# 第八章 对游戏的输入进行反馈吧

[>>点此回到教程目录](pro_doc.md)

按键配置对STG而言是非常重要的部分。

如果我们在最开始不进行设定的话，那么随着后面源程序的整体地改变，事情也会变得麻烦，因此我们在最开始就设定好吧。

首先我们要获得按键的输入状态。（这一点我们在之前的小节中已经完成了）

其次要取得手柄的输入状态。由于我们希望无论是键盘输入还是手柄输入我们都有相应的回馈，因此我们将键盘的输入状态放到在config中设定好的手柄信息中，从而两方面我们都能给出回馈。

现在我们使用GetJoypadInputState函数，如果不清楚怎么用的，请到DX Libaray参考文档处确认一下。

那么，我们定义手柄和config会使用到的结构体，在结构体中我们把PAD_MAX设定到16，定义如下：

--在struct.h 如下追加--
```cpp
//和手柄相关的参数
typedef struct{
        int key[PAD_MAX];
}pad_t;
 
//和config相关的文件
typedef struct{
        int left,up,right,down,shot,bom,slow,start,change;
}configpad_t;
```
–define.h变更–
```cpp
//区域的尺寸
#define FIELD_MAX_X 384
#define FIELD_MAX_Y 448
//区域的左上角坐标
#define FIELD_X 32
#define FIELD_Y 16
//手柄按键的最大数量
#define PAD_MAX 16
 
#include "struct.h"
```
我们先预设定在手柄上使用到的按键数目最大为16个，首先我们确定其中的九个按键分别表示“左、上、右、下、发射、Boom、低速运动、开始、变身”。

对于不同的键位使用0~15中的数字去对应，这样一来就可以按照自己喜好的设定来使用手柄上的按键了。

至于手柄上的哪个编号和哪个按钮对应，实际上这需要自己去确认。

就我这边确认的结果，一般使用下述的编号，我们在初始化函数中带入这些值。

–ini.cpp变更–
```cpp
#include "../include/GV.h"
 
//最开始的初始化
void first_ini(){
        ch.x=FIELD_MAX_X/2;
        ch.y=FIELD_MAX_Y*3/4;
 
        configpad.down=0;
        configpad.left=1;
        configpad.right=2;
        configpad.up=3;
        configpad.bom=4;
        configpad.shot=5;
        configpad.slow=11;
        configpad.start=13;
        configpad.change=6;
}
```
–向GV.h 进行以下追加–
```cpp
GLOBAL configpad_t configpad;
```
	
使用GetHitPadStateAll函数可以获得当前手柄的输入状态，与键盘输入状态进行比较，取用按键时间较长的一方，然后带入手柄的输入变量中。此时对上面的两个结构体并没有做任何更改。

在这个时候将使用的参数1和参数2中较大的一方放到参数1中的函数是这样的：
```cpp
void input_pad_or_key(int *p, int k);
```	
如果您对在这个函数中使用的三元表达式不熟悉的话，请自行搜索相关资料。

如果向CheackStatePad函数传入在初始化函数中设定好了的手柄编号，那么它会返回其输入状态。

它和已经实现好了的CheckStateKey函数是一个功能。

–key.cpp变更–
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
//保存手柄输入状态的变量
pad_t pad;
 
//把参数1和参数2中较大的一方传给参数1
void input_pad_or_key(int *p, int k){
        *p = *p>k ? *p : k;
}
 
//同时检查手柄和键盘输入的函数
void GetHitPadStateAll(){
        int i,PadInput,mul=1;
        PadInput = GetJoypadInputState( DX_INPUT_PAD1 );//获得手柄的输入状态
        for(i=0;i<16;i++){
                if(PadInput & mul)  pad.key[i]++;
                else                pad.key[i]=0;
                mul*=2;
        }
        input_pad_or_key(&pad.key[configpad.left]   ,CheckStateKey(KEY_INPUT_LEFT    ));
        input_pad_or_key(&pad.key[configpad.up]     ,CheckStateKey(KEY_INPUT_UP      ));
        input_pad_or_key(&pad.key[configpad.right]  ,CheckStateKey(KEY_INPUT_RIGHT   ));
        input_pad_or_key(&pad.key[configpad.down]   ,CheckStateKey(KEY_INPUT_DOWN    ));
        input_pad_or_key(&pad.key[configpad.shot]   ,CheckStateKey(KEY_INPUT_Z       ));
        input_pad_or_key(&pad.key[configpad.bom]    ,CheckStateKey(KEY_INPUT_X       ));
        input_pad_or_key(&pad.key[configpad.slow]   ,CheckStateKey(KEY_INPUT_LSHIFT  ));
        input_pad_or_key(&pad.key[configpad.start]  ,CheckStateKey(KEY_INPUT_ESCAPE  ));
        input_pad_or_key(&pad.key[configpad.change] ,CheckStateKey(KEY_INPUT_LCONTROL));
}
 
//返回传入的手柄的按键编号对应的输入状态。如果返回-1表示传入错误。
int CheckStatePad(unsigned int Handle){
        if(0<=Handle && Handle<PAD_MAX){
                return pad.key[Handle];
        }
        else{
                printfDx("向CheckStatePad传入了错误的键值\n");
                return -1;
        }
}
```
为了向input_pad_or_key函数传入各种参数写了一大堆东西，事实上如果使用数组和指针的话会省事许多，如果您尚有余力，那么可以尝试把这段代码简短一下。

–char.cpp变更–
```cpp
#include "../include/GV.h"
 
void calc_ch(){
        ch.cnt++;
        ch.img=(ch.cnt%24)/6;
        if(CheckStatePad(configpad.left)>0)//如果左键按下的情况
                ch.x-=3;//坐标向左增加
        if(CheckStatePad(configpad.right)>0)//如果右键按下的情况
                ch.x+=3;//坐标向右增加
}
```
CheckStatePad函数的使用如上。因为设定好了的按键，比如左键的编号已经放进了configpad.left中，所以就直接把这个编号传给CheckStatePad函数。因为这个函数会返回表示按键按下时间长短的计数器的值，所以如果这个值不是0的话，那说明就没有按下。

接下来我们把这次增加的函数向头文件里追加进去吧。

–向function.h 进行如下追加–
```cpp
GLOBAL void GetHitPadStateAll();
GLOBAL int CheckStatePad(unsigned int Handle);
```
在本次的main文件中发生变化的部分在下面用红字标出。

–main.cpp变更–
```cpp
#define GLOBAL_INSTANCE 
#include "../include/GV.h"
 
//循环必须处理的三大过程
int ProcessLoop(){
    if(ProcessMessage()!=0)return -1; //过程处理如果发生错误返回-1
    if(ClearDrawScreen()!=0)return -1; //如果画面清空错误返回-1
    GetHitKeyStateAll_2();//进行当前按键处理
 
    /*** 修改请注意 ***/
    GetHitPadStateAll();  //处理当前的手柄输入
    /*** 修改请注意 ***/
 
    return 0;
}
 
int WINAPI WinMain(HINSTANCE hInstance,HINSTANCE hPrevInstance,LPSTR lpCmdLine,int nCmdShow){
    ChangeWindowMode(TRUE);//窗口模式
    if(DxLib_Init() == -1 || SetDrawScreen( DX_SCREEN_BACK )!=0) return -1;//初始化和里表面化
 
    while(ProcessLoop()==0){//主循环
        switch(func_state){
            case 0:
                load();        //载入数据
                first_ini();//最开始的初始化
                func_state=100;
                break;
            case 100:
 
                /*** 修改请注意 ***/
                calc_ch();       //角色计算
                /*** 修改请注意 ***/
 
                graph_main();//绘制main
                break;
            default:
                printfDx("错误的func_state\n");
                break;
        }
 
        if(CheckStateKey(KEY_INPUT_ESCAPE)==1)break;//按下ESC键则跳出循环
        ScreenFlip();//里外画面翻转
    }
 
    DxLib_End();//DX Library终止处理
    return 0;
}
```
---
运行结果

<embed src="http://player.youku.com/player.php/Type/Folder/Fid/23155717/Ob/1/sid/XODQxMzQwODUy/v.swf" quality="high" width="480" height="400" align="middle" allowScriptAccess="always" allowFullScreen="true" mode="transparent" type="application/x-shockwave-flash"></embed>

---
接入游戏手柄然后左右按一下，然后在键盘上用十字键左右按一下，如果角色开始如同动画中那样地左右移动的话那就成功了。

[>>点此回到教程目录](pro_doc.md)