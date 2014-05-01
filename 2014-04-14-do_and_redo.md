
[categories] programming


在学maya/MPxCommand, 当一个操作需要支持undo/redo的时候，用户操作的次序可能是: 
- do sth -> undo it, or 
- do sth -> undo it -> redo it;
在实现的时候会发现do sth 跟 redo sth的代码基本一样啊，那当然想写一个都被do and redo调用的函数就好了，避免代码重复嘛。

maya/devkit的例子所使用的方法是:
```
//code  
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
