Title: Do and redo are similiar  
Date: 2014-04-14 10:20 
Modified: 2014-04-14 19:30 
Category: programming 
Tags: 
Slug: Do and redo are similiar  
Authors: 
Summary: Do and redo are so similiar that they can share code.


在学maya/MPxCommand, 当一个操作需要支持undo/redo的时候，用户操作的次序可能是: 
- do sth -> undo it, or 
- do sth -> undo it -> redo it;
在实现的时候会发现do sth 跟 redo sth的代码基本一样啊，那当然想写一个都被do and redo调用的函数就好了，避免代码重复嘛。

maya/devkit的例子所使用的方法是:
```
//code  
// inside class CommandXY 
virtual bool isUndoable() const { return true or false; }
virtual MStatus doIt( const MArglist & )
{
	// parse the argument list;
	// do some setting;
	return redoIt(); 
}
virtual MStatus redoIt() { do the actually command operation here }; 
virtual MStatus undoIt() {}; 
``` 
当用户按键例如Ctrl+Z做完undo, 又觉得需要redo回去, 那么 Shift+Z做redo的时候，这个operation的setting其实是沿用了之前做doIt时候的setting，所以doIt()比redoIt()多了一两步而已。

这个do/redo函数里面的分工问题，在以前的Mudbox project中就遇到过，当时的写法有点像:
```
// code
enum Status { DO, UNDO, REDO } status; 
virtual bool CustomOp::ExecuteAndInvert( void )
{
    if (status == DO) {}
	else if (status == UNDO) {}
	else (status == REDO) {} 
};
// for more details, check the mudbox sdk operation.h;
```

[2014/6/30 updated] 上面的command也好, operation也好, 是具体的某个需要支持undoable的"操作", 而要整体支持undo功能, 还需要管理这些command/operation的mamager/center/kernel随便怎么叫. 管理者的功能是把这些command/operation压栈和出栈, 并调用它们的相应函数(上面我们提到的只是一部分). 而这个管理者的实现中, 一般带一个栈stack的结构用于以特定次序存放这些cmd/op, 那么假如以管理者的角度或者是stack的角度来看那些cmd/op, 又是怎么一个过程呢? 具体请search我另一篇跟undo/redo相关的文章, 谢谢。


