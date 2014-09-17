Title: Pointer is copied when passed as function parameter
Date: 2014-09-12 12:00
Modified: 2014-09-12 12:00 
Category: programming
Tags: 
Slug: Pointer is copied when passed as function parameter
Authors: renc


起源  
----   
看到这段代码(肯定是简化更改版啦)  
```   
void XList::merge(Item *item)   
{  
    ... // do the merge sth;   
    item->unref();  
}   
void XList::merge(Item &item)   
{  
    ... // do the merge sth;   
}   
```     
上面的unref()是用于reference count的, reference计数减一的意思. 为什么有这东东呢, 为什么下面用引入作为参数的就不需要呢? 
我怀疑是指针在作为函数参数的传递过程中被复制了, 也就是指向实际的物体的指针又多了一个, 所以merge之后就unref减少一个. 以下的test case尝试证明指针是否被复制了:   
```    
// try to check if a pointer will be copied during passing-by-pointer.         
//              
#include <iostream>             
                   
void func1(int *p)           
{               
	std::cout << "in func1: " << *p << ", " << &p << ", " << p << ", " << &(*p) << std::endl;                 
	*p = 1;                
}                      
void func2(int &p)                    
{            
	std::cout << "in func2: " << p << ", " << &p << std::endl;        
	p = 2;            
}          
            
class B             
{          
public:               
	B() : b(0) {};                  
	B(const B &_inB) { b = _inB.b; }             
	void operator=(const B &_inB) { b = _inB.b; }           
	              
private:           
	int b;                 
};                   
                       
void func3(B *p)              
{                 
	std::cout << "in func3: " << &p << ", " << p << ", " << &(*p) << std::endl;             
}                          
void func4(const B &p)                
{                                
	std::cout << "in func4: " << &p << std::endl;             
}              
              
int main()            
{                
	int a = 0;                  
	std::cout << "init: " << a << ", " << &a << std::endl;        
	func1( &a );       
	func2( a );            
	     
	B Binstance;        
	std::cout << "init: " << &Binstance << std::endl;           
	func3( &Binstance );           
	func4( Binstance );             
	             
	return 1;          
}            

```     
代码中有意思的地方是对于指针来说, 怎么输出当前变量自己的地址和变量自己所指向的物体的地址. output:                 
init: 0, 00E5FAC8            
in func1: 0, 00E5F9E8, 00E5FAC8, 00E5FAC8          
in func2: 1, 00E5FAC8              
init: 00E5FABC            
in func3: 00E5F9E8, 00E5FABC, 00E5FABC     
in func4: 00E5FABC       
具体的source at github\coding_exercises\pointer_unref      
            