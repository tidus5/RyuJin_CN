# 第一章 准备好项目和素材吧

[>>点此回到教程目录](pro_doc.md)

龙神录程序馆以Visual Studio 2005或者Visual Studio 2008作为编译工具。

当然，其它的编译器也可以进行编译，但是发布的工程使用的是Visual Studio的工程文件，因此使用Visual Studio将会更加方便。

龙神录使用的素材都已经放进了下面的工程里了。

[下载所有的项目文件](http://pan.baidu.com/s/1c0u7cFu)

文件夹的构成如下所示：
![](http://dixq.net/rp/img/1/3.png)

在上图中写着“1章”的文件夹中，用于放置许多追加的东西。

![](http://dixq.net/rp/img/1/5.png)

今后每追加一章就会追加相应的文件夹。

请点开每一章中的RyuJin.sln文件。

![](http://dixq.net/rp/img/1/6.png)


dat文件夹存放了图片、音乐、效果音等和游戏一起发布的数据。  
mydat文件夹里放的是我自己添加的头文件、源文件、Photoshop的文件等自己使用的数据。  
Include文件夹中放的是DX Library的文件夹。  


请点开顶端的“RyuJin.sln”文件。  
项目的基本框架，就是由这些文件构成的。

![](http://dxlib.irisine.com/lib/exe/fetch.php?tok=cc62d6&media=http%3A%2F%2Fdixq.net%2Frp%2Fimg%2F1%2F1.png)

观察解决方案管理器，会发现文件是这样子构成的。

<font color=#FF0000 size=12>哇，怎么这么多文件。</font>

嘛，不用担心。现在我们什么都还没写呢。无管哪个文件都是一行代码都没有写的。

所以还没有必要去理解它们的意义。

要问为什么只有Hard Mode（译者注：玩过东方都知道这模式吧……），那是因为如果我们一开始就学会了做最复杂的弹幕的话，那么做简单的弹幕那就非常轻松了。  

先做出最复杂的弹幕，然后减少弹的数量或者将轨道进行简化，进行这样子的工作会很容易；但反过来，先做出简单的弹幕，要将其复杂化，那可就不知道要怎么去增加弹的数量才好了，毕竟为了增加弹的数量，很容易破坏整体的平衡性，因此我们一开始就先做很复杂的弹幕吧。

这一小节仅仅能够解说“工程原来有这么一些文件”这一点已经足够了。  

如果您很在意文件的构成，那么请参考下面对各个文件的介绍。  

从图片中由上至下按顺序：

|文件名	  |用途  |
| ------------- |-----|
|boss_shotH.cpp	 |为Boss的弹幕而设置的文件（Hard）|
|boss_shot.cpp	|控制Boss弹幕的文件|
|enemy.cpp	|控制杂兵运动的文件|
|enemy_act_pattern.cpp	|控制杂兵运动模式的文件|
|graph.cpp	|与绘制相关的文件|
|graph_back.cpp	|与背景绘制相关的文件|
|shotH.cpp	|为杂鱼的射击而设置的文件（hard）|
|cshot.cpp	|为自机的射击而设置的文件|
|shot.cpp	|控制杂兵射击的文件|
|char.cpp	|控制自机的文件|
|fps.cpp	|控制FPS的文件|
|ini.cpp	|初始化的文件|
|key.cpp	|控制按键的文件|
|load.cpp	|用于载入图片和音乐的文件|
|main.cpp	|main|
|music.cpp	|控制音乐的文件|
|out.cpp	|计算碰撞判定的文件|
|define.h	|定义用的头文件|
|function.h|	extern声明用的头文件|
|GV.h	|为全局变量设置的头文件|
|struct.h	|定义结构体用的头文件|

---

[>>点此回到教程目录](pro_doc.md)
