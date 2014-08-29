Title: choose the overrided function 
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: programming 
Tags: 
Slug: choose the overrided function  
Authors: 
Summary: In class hierarchy, if functions are overrided by some subclass, which one will be invoked? 

--- 
layout: post 
title: choose the overrided function 
categories: programming 
--- 

# 哪个override的函数才是啊? 

故事发生的背景：在看代码时，从父类到子类好几层啊，在上层(我以往父类方向跑为上)会定义一些virtual function, 在下层会override 某种上层的virtual function，这很正常。而当在上层的function(may or may not be virtual)中去调用一个virtual fuction的时候，到底是哪一层中的实现版本被真正调用了呢？

show me the code: 
下面我只是定义两层, ManipBase and ManipMove, 两个vritual functions interfaceA() and interfaceB(); 为什么用这些名字? maya api中不是有manipulator嘛, base表示父类, move表示用于移动物体的manipulator. 
这里，先看第一种情况Test1，然后稍微做了改动，得到Test2 and Test3. 
```
// code 
#include <stdio.h>

namespace Test1 {
class ManipBase
{
public:								   
	virtual void interfaceA()
	{
		printf("ManipBase::interfaceA.\n");
		interfaceB();
	}
	virtual void interfaceB()
	{
		printf("ManipBase::interfaceB.\n");
	}
};

class ManipMove : public ManipBase
{									
public:								   
	virtual void interfaceA()
	{
		printf("ManipMove::interfaceA.\n");
		interfaceB();
	}
	virtual void interfaceB()
	{
		printf("ManipMove::interfaceB.\n");
	}
};

void test()
{
	ManipBase *pManip = new ManipMove;
	pManip->interfaceA();
}					  
// the output result: 
//ManipMove::interfaceA.
//ManipMove::interfaceB.
}; // namespace

namespace Test2 {
class ManipBase
{
public:								   
	virtual void interfaceA()
	{
		printf("ManipBase::interfaceA.\n");
		interfaceB();
	}
	virtual void interfaceB()
	{
		printf("ManipBase::interfaceB.\n");
	}
};

class ManipMove : public ManipBase
{									
public:								   
	virtual void interfaceA()
	{
		printf("ManipMove::interfaceA.\n");
		interfaceB();
	}
};						 

void test()
{
	ManipBase *pManip = new ManipMove;
	pManip->interfaceA();
}					  
// the output result: 
//ManipMove::interfaceA.
//ManipBase::interfaceB.
}; // namespace

namespace Test3 {
class ManipBase
{
public:								   
	virtual void interfaceA()
	{
		printf("ManipBase::interfaceA.\n");
		interfaceB();
	}
	virtual void interfaceB()
	{
		printf("ManipBase::interfaceB.\n");
	}
};

class ManipMove : public ManipBase
{									
public:					
	virtual void interfaceB()
	{
		printf("ManipMove::interfaceB.\n");
	}
};

void test()
{
	ManipBase *pManip = new ManipMove;
	pManip->interfaceA();
}
// the output result: 
//ManipBase::interfaceA.
//ManipMove::interfaceB.
}; // namespace
					  
int main()
{				  
	Test1::test();
	Test2::test();
	Test3::test();
	
	return 1;
}
```
结果？
我的感觉是virtual function的调用会从"最底层"开始找，找到就执行，找不到就往上层找. 

这里的"最底层"有意思。例如上面例子中ManipBase *pManip = new ManipMove;那最底层就是ManipMove;
假如再加入一层ManipBase <- ManipMove <- ManipSuperFastMove :-) 
case 1: ManipBase *pManip = new ManipMove;那最底层还是ManipMove;
case 2: ManipBase *pManip = new ManipSuperFastMove;那最底层就是ManipSuperFastMove;

