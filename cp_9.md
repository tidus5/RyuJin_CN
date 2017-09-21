# 第九章 试着控制一下角色的移动吧

[>>点此回到教程目录](pro_doc.md)

现在我们试着让我们的角色可以在画面中可移动的区域内上下左右地移动。

由于斜向前进的时候不给给以1/√2的速度的话，一般而言合速度是不会为1的（译者注：这是一个简单的运动学问题，将斜向的速度视为合速度矢量并投影到xy方向上，若斜向速度要保持为1，那么xy速度都应该为1/√2的速度），因此斜向移动的时候，有必要在水平和竖直方向上分别对速度进行变化。

–char.cpp变更–
```cpp
#include "../include/GV.h"
 
void calc_ch(){
        ch.cnt++;
        ch.img=(ch.cnt%24)/6;
}
 
void ch_move(){//控制角色的移动
        int i,sayu_flag=0,joge_flag=0;
        double x,y,mx,my,naname=1;
        double move_x[4]={-4.0,4.0,0,0},move_y[4]={0,0,4.0,-4.0};//{左,右,下,上}的速度
        int inputpad[4];
        inputpad[0]=CheckStatePad(configpad.left); inputpad[1]=CheckStatePad(configpad.right);
        inputpad[2]=CheckStatePad(configpad.down); inputpad[3]=CheckStatePad(configpad.up);
 
        if(CheckStatePad(configpad.left)>0)//左键按下的情况
                ch.img+=4*2;//图像左移
        else if(CheckStatePad(configpad.right)>0)//右键按下的情况
                ch.img+=4*1;//图像右移
 
        for(i=0;i<2;i++)//左右部分
                if(inputpad[i]>0)//如果左右方向上有输入
                        sayu_flag=1;//设置左右移动的标志
        for(i=2;i<4;i++)//上下部分
                if(inputpad[i]>0)//如果上下方向有输入
                        joge_flag=1;//设置上下移动的标志
        if(sayu_flag==1 && joge_flag==1)//如果左右、上下方向都有输入说明斜向移动
                naname=sqrt(2.0);//将移动速度变为1/√2
 
        for(int i=0;i<4;i++){//4方向分量循环
                if(inputpad[i]>0){//i方向的键盘或者手柄有输入
                        x=ch.x , y=ch.y;//暂时存入当前的坐标
                        mx=move_x[i];   my=move_y[i];//将移动分量带入mx和my                  
                        if(CheckStatePad(configpad.slow)>0){//如果低速运动
                                mx=move_x[i]/3; my=move_y[i]/3;//将移动速度变为1/3
                        }
                        x+=mx/naname , y+=my/naname;//为当前坐标加上移动分量
                        if(!(x<10 || x>FIELD_MAX_X-10 || y<5 || y>FIELD_MAX_Y-5)){//如果计算结果在可移动范围内
                                ch.x=x , ch.y=y;//进行实际的移动
                        }
                }
        }
}
```
因为使用了sqrt函数，所以需要包含math.h头文件。

然后把ch_move函数添加到function.h文件中。

–在GV.h进行以下追加–
```cpp
#include "math.h"
```

–在fnction.h进行以下追加-
```cpp
GLOBAL void ch_move();
```
主函数部分只添加了红字部分。

–main.cpp变更
```cpp
#define GLOBAL_INSTANCE 
#include "../include/GV.h"
 
//循环必须处理的三大过程
int ProcessLoop(){
    if(ProcessMessage()!=0)return -1; //过程处理如果发生错误返回-1
    if(ClearDrawScreen()!=0)return -1; //如果画面清空错误返回-1
    GetHitKeyStateAll_2();//进行当前按键处理
    GetHitPadStateAll();  //处理当前的手柄输入
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
                calc_ch();      //角色计算
                /*** 修改请注意 ***/
 
                ch_move();      //控制角色的移动
                graph_main();//描绘main
                break;
            default:
                printfDx("错误的c_state\n");
                break;
        }
 
        if(CheckStateKey(KEY_INPUT_ESCAPE)==1)break;//按下ESC键则跳出循环
        ScreenFlip();//里外画面翻转
    }
 
    DxLib_End();//DX Library终结
    return 0;
}
```
---

运行结果

<embed src="http://player.youku.com/player.php/Type/Folder/Fid/23155717/Ob/1/sid/XODQxMzQwNzYw/v.swf" quality="high" width="480" height="400" align="middle" allowScriptAccess="always" allowFullScreen="true" mode="transparent" type="application/x-shockwave-flash"></embed>

---

接上游戏手柄然后左右上下按键测试，同时在键盘上也左右上下按键测试，如果角色开始动画那样地移动的话那就成功了。

[>>点此回到教程目录](pro_doc.md)