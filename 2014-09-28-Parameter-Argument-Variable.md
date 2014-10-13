Title: Difference between Parameter and Variabe and Argument      
Date: 2014-09-28 12:00   
Modified: 2014-09-28 12:00       
Category: programming          
Tags:              
Slug: Difference between Parameter and Variabe and Argument   
Authors:    
 
翻译过来的话, variable 是变量, parameter and argument 是参数的意思, 怎么区别使用呢?    
```   
int add(int a, int b)     // (int a, int b)叫argument list, a/b是parameter.  
{                         
    int c = a + b;        // c is variable.   
    return c;           
}          
                      
int x = 1;                // x对应自己的scope下的variables.       
z = add(x, 2);            // x/y作为函数参数时候就是arguments.    
```    
引用一下网上的解释: 
> A parameter is a variable in a method definition. When a method is called, the arguments are the data you pass into the method's parameters.  - Torbjörn Hansson  
> A parameter is the variable which is part of the method’s signature (method declaration). An argument is an expression used when calling the method.  - tranmq 
> A parameter is the variable which is part of the method’s signature (method declaration). An argument is an expression used when calling the method.  – vol7ron         
    
 