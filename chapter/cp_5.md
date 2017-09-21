# 第五章 琢磨一下声明的方式吧

[>>点此回到教程目录](pro_doc.md)

在上一小节中，main文件被我们改得乱七八糟的，这样一来，根本弄不清楚到底在哪里定义了变量，又在哪里使用了extern了。

这样一来，我们就得好好琢磨一下变量的定义和extern的声明方式了。

我们稍微在这里复习一下上一章讲过的内容。

1. （设计的时候）在main文件即main.cpp文件中定义了全局变量。
2. 想要在其它文件中调用这个全局变量的时候，那么在其它文件中进行extern声明。

大概就是这些内容了吧。

不过，在main文件中声明全局变量，而在其它文件中对同名的变量进行extern，总让人感觉没有什么开发效率。

反过来，如果我们能在所有的源文件中引入同一个头文件，而在这个头文件中对调用源进行条件分歧检查、以及进行适当的声明，那么就比较有效率了。

请看下述程序。
```cpp
#ifdef GLOBAL_INSTANCE
#define GLOBAL
#else
#define GLOBAL extern 
#endif
 
GLOBAL int img;
```
前半部分意味着
如果定义了“GLOBAL_INSTANCE”，那么有GLOBAL的地方就用空白代替。
如果没有定义“GLOBAL_INSTANCE”，那么有GLOBALE的地方就用extern代替。
这样一来如果我们直接写
```cpp
GLOBAL int img;
```
的话，结果会是如何的、也即是是否会加上extern这一点很容易就能清楚了。

因为在main.cpp中调用的时候全局变量必须被定义，所以我们不希望加上extern。

也即是定义GLOBAL_INSTANCE之后再调用就可以了。

一方面在其它文件中调用的时候我们希望加上extern。

也即是说不定义GLOBAL_INSTANCE，也就是我们什么都不写直接调用就行了。

请注意main文件的最上方。这里写着：
```cpp
#define GLOBAL_INSTANCE 
#include "../include/GV.h"
```
因为现在我们定义了GLOBAL_INSTANCE然后再引入头文件，那么就进行了变量的声明。

另一方面，在别的文件中调用main文件中的全局变量的话就不应该去定义它，因此添加上extern。

这样一来，在别的文件中也可以调用这个头文件了。

再者，如果我们要在其它文件中调用已经实现了的函数的话，那么我们也有必要在main文件中对其进行声明。

进行了extern声明的话，那么无论是在其实现的地方还是在其调用的地方它们的先后顺序都不存在问题，因此如果在main文件中声明了的话，那么无论在哪里实现，只要进行了extern声明那就可以调用。

进一步，对在进行了条件分歧的GV.h中的function.h中进行和上面同样的GLOBAL声明。

嘛，只是看的话肯定不容易理解，所以像下面这样写。

–function.h変更–
```cpp
//graph.cpp
        //绘制main
        GLOBAL void graph_main();
 
//key.cpp
        //处理当前的按键输入
        GLOBAL int GetHitKeyStateAll_2();
        //返回传入的按键的编号的当前状态
        GLOBAL int CheckStateKey(unsigned char Handle);
 
//laod.cpp
        //载入数据
        GLOBAL void load();
```
–GV.h变更–
```cpp
#include "../../../include/DxLib.h"
#include "define.h"
 
#ifdef GLOBAL_INSTANCE
#define GLOBAL
#else
#define GLOBAL extern 
#endif
 
#include "function.h"           //函数声明
 
//图像用变量声明部分
GLOBAL int img_ch[2][12];       //角色图像12张　X2(变身用)
 
//结构体变量声明部分
GLOBAL ch_t ch;                 //角色声明
```
然后我们把“main.cpp”、“graph.cpp”、“load.cpp”里的定义部分和extern声明都删掉。

编译执行，没有出错且效果和第四节中的效果相同。

今后我们如果要增加全局变量的话，就在GV.h中增加，而在main文件中要调用的其它文件中声明了的函数都在function.h中增加。

[>>点此回到教程目录](pro_doc.md)