Title: Bit field 
Date: 2014-11-10 20:20
Modified: 2014-11-10 22:30
Category: programming 
Tags: 
Slug: Bit field 
Authors: 

一般在C++代码里面遇到的基本类型bool, char, short, int, etc, 都是说占多少个字节, 也就是最少也是一个字节. 那有时候会不会1个字节就多余了, 或者说1个字节不单只可以表示true/false, 还可以表示更多的信息呢?  毕竟1个字节=8bits=2^8=256个状态.           
          
## Part 1           
          
Bit field 貌似就是想充分利用内存. 下面是我看了参考(Reference那一节列了一些)之后做的实验:            
```            
#include <cstdio>          
          
// 交通繁忙时间            
enum CallTariff { tOffPeak, tMediumRate, tPeakTime } ;           
          
// 账单           
struct Bill           
{          
	unsigned int	iNameId;    // 客户名           
	CallTariff		eTariff;           
};           
          
struct BillVIP           
{          
	unsigned int	iNameId;    // 客户名           
	bool			isVIP;           
	CallTariff		eTariff;           
};           
           
// optimization 1.           
namespace Op1 {          
	          
	struct Bill           
	{          
		unsigned int iNameId;          
		unsigned int iTraffic : 2; // range: [0, 3];           
	};           
	
	struct Bill2  
	{         
		unsigned int iNameId;         
		unsigned int iTraffic : 2; // range: [0, 3];           
		unsigned int iTraffic2 : 3; // range: [0, 8];             
	};         
                   
	struct BillVIP                
	{                    
		unsigned int	iNameId;    // 客户名                     
		bool			isVIP;           
		unsigned int	iTariff : 2;           
	};          
	struct BillVIP2           
	{                    
		unsigned int	iNameId;    // 客户名                     
		unsigned short	isVIP    : 1;           
		unsigned short	iTariff  : 2;           
	};          
	struct BillVIP3           
	{          
		unsigned int	iNameId;    // 客户名           
		unsigned int	isVIP    : 1;                     
		unsigned short	iTariff  : 2;           
	};          
	struct BillVIP4           
	{          
		unsigned int	iNameId : 3;    // 客户名           
		unsigned int	isVIP   : 1;           
		unsigned int	iTariff : 2;           
	};          
	struct BillVIP5          
	{          
		unsigned short	iNameId : 13;    // 客户名           
		unsigned short	isVIP   : 1;           
		unsigned short	iTariff : 2;           
	};          
	struct BillVIP6          
	{          
		unsigned short	iNameId : 8;    // 客户名           
		unsigned short	isVIP   : 7;           
		unsigned short	iTariff : 7;           
	};          
	class BillVIP7          
	{          
	public:          
		unsigned short	iNameId : 8;    // 客户名           
		unsigned short	isVIP   : 7;           
		unsigned short	iTariff : 7;           
	};          
          
};           
          
int main()          
{          
	// to check the sizeof some basic types.           
	CallTariff t;	          
	printf("sizeof CallTariff: %d, %d\n", sizeof(CallTariff), sizeof(t));           
          
	printf("sizeof bool: %d\n", sizeof(bool));           
          
	{          
		Bill b;           
		printf("sizeof Bill: %d, %d\n", sizeof(Bill), sizeof(b));            
          
		BillVIP bVIP;           
		printf("sizeof BillVIP: %d, %d\n", sizeof(BillVIP), sizeof(bVIP));            
	}          
          
	{          
		printf("\n----op1----\n");            
		          
		Op1::Bill b;           
		b.iTraffic = CallTariff::tOffPeak;           
		printf("sizeof Bill: %d, %d\n", sizeof(Op1::Bill), sizeof(b));            
          
		Op1::Bill2 b2;           
		b2.iTraffic = CallTariff::tOffPeak;           
		printf("sizeof Bill: %d, %d\n", sizeof(Op1::Bill2), sizeof(b2));          
          
		          
		Op1::BillVIP bVIP;           
		bVIP.isVIP = true;          
		printf("sizeof BillVIP: %d, %d\n", sizeof(Op1::BillVIP), sizeof(bVIP));           
		          
		Op1::BillVIP2 bVIP2;           
		bVIP2.isVIP = true;          
		printf("sizeof BillVIP2: %d, %d\n", sizeof(Op1::BillVIP2), sizeof(bVIP2));           
		          
		Op1::BillVIP3 bVIP3;           
		printf("sizeof BillVIP3: %d\n", sizeof(Op1::BillVIP3));          
		Op1::BillVIP4 bVIP4;           
		printf("sizeof BillVIP4: %d\n", sizeof(Op1::BillVIP4));           
		Op1::BillVIP5 bVIP5;           
		printf("sizeof BillVIP5: %d\n", sizeof(Op1::BillVIP5));           
		Op1::BillVIP6 bVIP6;           
		printf("sizeof BillVIP6: %d\n", sizeof(Op1::BillVIP6));           
		Op1::BillVIP7 bVIP7;             
		printf("sizeof BillVIP7: %d\n", sizeof(Op1::BillVIP7));          
	}            
                   
	return 1;            
}          
```           
先看一遍代码，基本上就是尝试判断不同struct所占的字节大小. 我机器下的结果如下: 
```  
sizeof CallTariff: 4, 4
sizeof bool: 1
sizeof Bill: 8, 8
sizeof BillVIP: 12, 12

----op1----            
sizeof Bill: 8, 8                
sizeof Bill2: 8, 8                 
sizeof BillVIP: 12, 12              
sizeof BillVIP2: 8, 8             
sizeof BillVIP3: 12        
sizeof BillVIP4: 4         
sizeof BillVIP5: 2          
sizeof BillVIP6: 4          
sizeof BillVIP7: 4          
```           
上面的结果还是挺有意思的。非常类似的结果struct，里面通过不同的类型(short, int)的安排, 以及各个数位的安排, 可以改变这个struct的结果.             
           
下面在仔细分析一下, 代码跟上面是一样的, 只是我加了注释:            
```              
struct Bill              
{                  
	unsigned int	iNameId;    // 客户名                   
	CallTariff		eTariff;                       
};                          
这个unsigned int = 4 bytes, CallTariff = 4 bytes, sum = 8 bytes.                         
               
struct BillVIP         
{                   
	unsigned int	iNameId;    // 客户名                 
	bool			isVIP;           
	CallTariff		eTariff;               
};           
这个unsigned int = 4 bytes, bool = 1 bytes, CallTariff = 4 bytes, sum = 9 bytes.   但是结果是12的，那是因为对齐。              
                         
// optimization 1.                 
namespace Op1 {                   
	                      
	struct Bill                             
	{               
		unsigned int iNameId;                        
		unsigned int iTraffic : 2; // range: [0, 3];                      
	};                    
    unsigned int iNameId = 4 bytes, 然后看到 unsigned int iTraffic:2; 不是说分配2 bits, 而是先分配unsigned int = 4 bytes, but 只用了其中2bits. 所以还是一共 8 bytes.                      
                     
	struct Bill2             
	{                 
		unsigned int iNameId;        
		unsigned int iTraffic : 2; // range: [0, 3];                  
		unsigned int iTraffic2 : 3; // range: [0, 8];                  
	};                    
    第一个unsigned int = 4 bytes; 第二个unsigned int再分配第二个4bytes, 其中用了2 bits; 看到第三个unsigned int的时候, 因为只用3bits, 前面第二个还没有用完, 而且再加上这3bits够用有余. 所以第三个unsigned int就没有分配空间.                                   
                          
	struct BillVIP               
	{                
		unsigned int	iNameId;    // 客户名                                 
		bool			isVIP;                
		unsigned int	iTariff : 2;                 
	}; 中间的bool也是因为对齐 而占了 4bytes?                 
                         
	struct BillVIP2                      
	{                                                              
		unsigned int	iNameId;    // 客户名           
		unsigned short	isVIP    : 1;                  
		unsigned short	iTariff : 2;            
	};                                  
    这里我以为是4bytes + 2bytes = 6 bytes的。但不是.             
                                
	struct BillVIP3                
	{                     
		unsigned int	iNameId;    // 客户名             
		unsigned int	isVIP    : 1;               
		unsigned short	iTariff : 2;          
	};            
    这里也不像我才的4 bytes + 4 bytes + 2 bytes = 10 bytes. 好像结果是要是struct里面最大的元素4的倍数?      
                 
	struct BillVIP4           
	{               
		unsigned int	iNameId : 3;    // 客户名              
		unsigned int	isVIP   : 1;          
		unsigned int	iTariff : 2;            
	};             
    这个有意思，首先全部元素都是一样的, 分配了一个unsigned int = 4 bytes就够用了.             
                  
	struct BillVIP5                
	{                  
		unsigned short	iNameId : 13;    // 客户名                 
		unsigned short	isVIP   : 1;             
		unsigned short	iTariff : 2;       
	};                   
    这里一个unsigned short = 2 bytes就够用了.             
                    
	struct BillVIP6                 
	{                  
		unsigned short	iNameId : 8;    // 客户名             
		unsigned short	isVIP   : 7;                 
		unsigned short	iTariff : 7;             
	};                        
    这里为什么不是2 bytes, 3 bytes, 而是4bytes呢?                
                                                   
	class BillVIP7               
	{                     
	public:            
		unsigned short	iNameId : 8;    // 客户名                           
		unsigned short	isVIP   : 7;                  
		unsigned short	iTariff : 7;                
	};                                
    class与struct一样.        
                            
};              
```    
上面的分析可见我有部分是不甚了解, 可能有更深的技术细节。但反应的一个问题就是不直观.           
                         
References                
----                   
http://publications.gbdirect.co.uk/c_book/chapter6/bitfields.html                 
http://www.informit.com/guides/content.aspx?g=cplusplus&seqNum=131          
http://en.wikipedia.org/wiki/Bit_field                                   
                                              
## Part 2                       
假如我们要表达一个重要的信息: 有100个元素, 每个元素就两种状态, 例如true or false, sel ected or not selected, visited or not visited... 那么用上面的bit field语言细节可能:               
```                                                                        
struct SelectionField {                           
    unsigned char element1 : 1;                        
    unsigned char element2 : 1;                        
    unsigned char ...                        
    unsigned char element8 : 1;               
}; // 1 bytes;                          
                         
SelectionField arr[16];                             
bool test(unsigned int index) {...};                   
```                                          
然后使用的时候, 例如要判断第93号元素是否为true/false;                  
a = index / 16;                                            
b = index % 8;                   
check arr[a].element(b)'s value; 也就是a = 5, b = 3, arr[5].element3.      
                       
原来在STL里面有一个std::bitset<>; 它的实现跟上面的理解也有点类似:    
```                                    
bitset<_Bits>& set(size_t _Pos,                   
	bool _Val = true)                 
	{	// set bit at _Pos to _Val              
		if (_Bits <= _Pos)                    
			_Xran();	// _Pos off end              
		if (_Val)                          
			_Array[_Pos / _Bitsperword] |= (_Ty)1 << _Pos % _Bitsperword;          
		else                 
			_Array[_Pos / _Bitsperword] &= ~((_Ty)1 << _Pos % _Bitsperword);           
		return (*this);               
	}                        
```    
首先<< 与 %的优先级问题: modulus % 先于 bitwise shift;    
(_Ty)1 << _Pos % _Bitsperword == (_Ty)1 << (_Pos % _Bitsperword) 也就是先取余数.    
然后就是bitwise shift的操作, 例如取余数得到3, 然后1<<3 = 1000(二进制binary format), 来跟 _Array[a]做操作....                                         
     
这里的_Array[a] 跟我们上面的SelectionField arr[xx]很像, 但是上面的arr[xx]是一个很tricky的structure, 需要我们自己来定义里面是8个bits组成的 1 bits x 8, 而在_Array[a]这里, 却不需要这么麻烦, 只要我们保证_Array[a]是一个_Bitsperword的类型就ok了, 例如当是一个_Bitsperword的类型就ok了=8的时候里面可能就是一个unsigned char.        

应用。学习了这个数据结构, 在日常中有什么用呢?    
例如我们要在mesh上记录一堆selected faces, 就有以下两种不同的存储方式:         
std::vector<unsigned int> aSelectedFaces ( selected faces's count);           
std::bitset<mesh.FaceCount()> faceFlags;      
具体哪种比较好? .... 

---- 
用perforce的时候, 怎么看一个workspace当前所处哪个changeset呢?  就是一个workspace放旧了，都忘了它之前sync到哪个changeset了.   
```   
C:\Workspace\ProjectX\src>p4 changes -m1 @my_workspace_at_local  
```  
