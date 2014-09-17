Title: Note of Maya programming 
Date: 2014-04-01 10:20
Modified: 2014-04-01 19:30
Category: programming 
Tags: 
Slug: Note of Maya programming
Authors: 
Summary: 学maya时候有些新概念要记录一下，否则忘得快. 
 

引言
---- 
学maya时候有些新概念要记录一下，否则忘得快. 


Command 
---- 
定义命令cmd的语法, 例如你可能想给命令添加一些flags. [ MPxCommand::newSyntax() ]
有了语法, 在具体执行这个命令的时候, 首先当然就是分解命令, 看它是否包含了哪些参数. [doIt, parseArgs, argument list, argument data]

分regular command and interactive command (tool command)两种, 分别对应MPxCommand and MPxToolCommand. 

ls -sl  # to list objects /components are selected. 
ls -hilite  # to list the objects ready be select their components 
ls -psh  # the current pre-select highlight coomponent. 
 
check the hilite command too. 

Context 
---- 
例如左边select tool, paint select tool, move tool...还有polygon/drag a cube等，其实每一样都是一个context，于是context好像跟tool工具是一个意思。
mel 下 "currentCtx"询问当前context类型的object. 
grabUVContext // 调用这个context command to create an object of this context;
// Result, grabUVContext1; // object的名字是grabUVContext1;
setToolTo grabUVContext1; // switch from current ctx to this new context object;

context涉及的范围很广:
+ context command, 也就是在plugin里面注册register一个context时候，其实是一个ctx command. 上面那个grabUVContext其实是一个cmd，而且是一个特殊的cmd类型, ctx cmd; -q 也就是query下也是作为一个cmd来用. 
+ context and interactive command, 例如moveTool;         
+ context and manipulator; 例子很多.               
              

UI   
----  
2014/7 因为要在maya界面的某个shelf上加button, 于是需要了解一下mel里面怎么建ui. 
大致是一个window, 下面是layout, 可以是各种各样的layout,  layout之间可能有父子关系，然后是button. 
当前知道的button有:
shelfButton 就是你在界面上Polygon下面可以点击了就建模型的那种;
toolButton 就是界面左边的select move rotate scale等. 
![Alt text](data/2014-06-25-UndoRedoStack.png "output")
UI例子 window/tabLayout/shelfLayout/shelfButton; 用setParent .. 可以回到上一级layout. 

那么怎么add custom shelf or shelf button 呢?

方法1. 找到maya安装路径\scripts\startup\目录, 看见下面很多shelf_xxx.mel, 例如shelf_Polygons.mel, shelf_Surfaces.mel, ...这些就是maya启动时候加载的, 也就是我们在界面上看到的.
那么我们就可以尝试在这路径下添加我们自己写的shelf_yyy.mel了。
或者是试试“MAYA_SHELF_PATH” environment variable?

方法2. 

References: 
+ mel command document: window, tabLayout, shelfLayout, shelfButton. 