Title: Pointer to Member Function  
Date: 2014-05-12 10:20
Modified: 2014-05-12 19:30
Category: programming 
Tags: 
Slug: Pointer to Member Function 
Authors: 
Summary: In the middle of time and space

--- 
layout: post 
title: Pointer to Member Function 
categories: programming 
--- 

function pointer是知道的, 如
``` 
int max( int a, int b) { ... };
 
int (*pFtor)( int, int ) = &max;
int iResult = pFtor(1, 2); 

or

typedef int(*FtorType)(int, int); 
FtorType pFtor; 
pFtor = &max;
int iResult = pFtor(1, 2);
``` 

但是当看到类似以下代码时候还是恐惧了一下: 
``` 
class Tool {
	int doA(ArgumentType &);
	int doB(ArgumentType &); 
}; 
typedef int(Tool::*MemberFunctionPtr)(ArgumentType &);


Tool toolObj;
MemberFunctionPtr pFtorA = &Tool::doA; 
MemberFunctionPtr pFtorB = &Tool::doB; 
 
int iResultA = (toolObj.*pFtorA)(argumentList); 
int iResultB = (toolObj.*pFtorB)(argumentList); 

or
Tool *pToolObj;
int iResultA = (pToolObj->*pFtorA)(argumentList); 

``` 

Google了一下，原来是pointer to member function. 一般pointer是指向一个obsolete address, 但是这个pointer to member function, 像是指向一个类class里面的偏移地址offset而已。给出一个对象(知道类的类型)，+这个offset, 推导出具体是某个函数? 

更复杂的情况，也是更常用的情况是引入Tool的子类derived class;
```  
class ToolX {
	// override doA and doB functions;
};
class ToolY {
	// override doA and doB functions;
};

ToolX toolXObj;
ToolY toolYObj; 
MemberFunctionPtr pFtorA = &Tool::doA; 
MemberFunctionPtr pFtorB = &Tool::doB; 
 
int iResultXA = (toolObjX.*pFtorA)(argumentList);  // ? 是调用了member func from base class or derived class呢? 
int iResultYA = (toolObjY.*pFtorA)(argumentList);  // ?

```   

OK，简单知道了有这样的技术以后，那么, 这种技术有什么用呢? 
Function pointer貌似很多情况下都是用于作为回调函数callback function, 目的是灵活. 这种pointer to member function也可以这么用。
``` 
typedef int(Tool::*MemberFunctionPtr)(ArgumentType &);

class Tool {
	
	ToolCallback *buildToolClassback() {
		ToolCallback *callback(*this, &Tool::doA);
		return callback;
	}

	int doA(ArgumentType &);
	int doB(ArgumentType &); 
}; 

class ToolCallback {
	ToolCallback( Tool &_toolObj, MemberFunctionPtr _p) 
	: toolObj(_toolObj), pFtor(_p) {}
	
	int do(ArgumentType) { (toolObj.*pFtor)( argument ); }

	Tool &toolObj; 				// 组成1: an object;
	MemberFunctionPtr pFtor;	// 组成2: on offset; 
}; 

// 后面我们就可以把ToolCallback *callback在代码里面传来传去, 
// 最后我们想执行这个东西了, 那就
callback->do(argument); 
``` 