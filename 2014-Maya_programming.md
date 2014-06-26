[categories] computer graphics, programming,

引言
---- 
学maya时候有些新概念要记录一下，否则忘得快. 


Command 
---- 
定义命令cmd的语法, 例如你可能想给命令添加一些flags. [ MPxCommand::newSyntax() ]
有了语法, 在具体执行这个命令的时候, 首先当然就是分解命令, 看它是否包含了哪些参数. [doIt, parseArgs, argument list, argument data]

Context 
---- 
例如左边select tool, paint select tool, move tool...还有polygon/drag a cube等，其实每一样都是一个context，于是context好像跟tool工具是一个意思。
mel 下 "currentCtx"询问当前context类型的object. 
texGrabContext // 调用这个context command to create an object of this context;
// Result, texGrabContext1; // object的名字是texGrabContext1;
setToolTo texGrabContext1; // switch from current ctx to this new context object;

 