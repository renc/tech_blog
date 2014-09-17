Title: conflict by duplicated variables 
Date: 2014-08-18 10:20
Modified: 2014-08-18 19:30
Category: programming 
Tags: 
Slug: conflict by duplicated variables 
Authors: 

用多于一个变量来表示一个状态, 变量之间很容易不一致呢.      
> conflict  
                 
假如我们要描述以下这个状态:          
在一次mouse click的picking中, 我们给出了pick的范围, 一个rectangle, 然后要标记是否只要pick一个物体object就够了, 还是pick所有在rectangel下面的物体.  
         
当假如我们只要pick到一个物体就够了, 那可能在for里面, 一旦找到某一个物体有效, 我们就可以返回, 退出pick过程了. 也就是我们只要pick到第一个有效物体就ok了, 而不考虑这个pick到的物体是否距离mouse click的点最近.   
         
那么, 要记录这个状态, 我们看下面的实现:     
``` 
class XViewContext            
{               
public:                           
	PickingResult pick(...);                               
    void setPickSingle(bool b) { m_bPickSingle = b; };                  	
	bool pickSingle() const { return m_bPickSingle; };                
private:               
	bool m_bPickSingle;              	  
};      		             
PickingResult XViewContext::pick(...)           
{                    
	...                                
    if ( pickSingle() ) ...;                 
	else ...;         
}          
``` 	            	  
在这实现中, 是否要pick单一个物体的状态作为data member inside a class; 在函数使用这个状态时候就用那个类成员.    

so far so good, until ... 不知哪时出于某些需求, pick()函数的参数中增加了一个变量, 变为:   
```     
PickingResult XViewContext::pick(..., bool bPickSingle)           
{                    
	...                                
    if ( bPickSingle ) ...;                 
	else ...;         
}          
``` 	    
pick()函数里面用传入过来的参数bPickSingle而不是类成员变量pickSingle()了.                  

而我的想法是这样的结果会出现:           
+ bPickSingle 与 pickSingle()的值 可能不一致的. 于是就可能出现bug, 例如你想要bPickSingle == true, 但是pickSingle() == false, 这时候, pick的结果就跟pick()函数里面到底是用哪个来决定了, 而很多时候你是没有看到pick()函数的实现的，更坏地是pick()函数的实现中两个都使用了.     
+ 我们好像可以避免这样的不一致, 要么就只用成员变量pickSingle(), 要么就在类里面不维护这么一个成员变量, 而在每次调用pick()前面都声明一个局部变量bPickSingle.   
 
 