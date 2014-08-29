--- 
layout: post 
title: How to debug maya custom plugin 
categories: programming 
--- 

引言
----
> 涉及maya plugin开发的时候，怎么debug呢? 

Attach to maya process ... 寄生 
----
step 1. maya plugin manager (Window -> Settings/Preferences -> Plug-in Manager)中load your custom plugin. This plugin need to be built by the same compiler version of maya, 也许不需要. 
step 2. at your visual studio project of that custom plugin, Tools -> Attach to Process ..., choose maya.exe you will see visual studio processing some source files....这就完整寄生了. 

Debug
---- 
+ MGlobal::displayInfo(MString("..")); 输出到Script Editor. 

+ MStatus status; 这东西是很多函数的返回值，记得判断是否MS::kSuccess. 用MString MStatus::errorString() const函数方便输出函数的返回状态. 

+ 