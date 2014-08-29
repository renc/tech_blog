Title: Vector based containers     
Date: 2014-05-13 10:20
Modified: 2014-05-13 19:30
Category: programming 
Tags: 
Slug: Vector based containers     
Authors: 
Summary: 在代码中std::vector经常被用到吧? 因为它很容易用, 只管往里面塞东西就ok了. 但是用!=用得好, 而对它的了解又是否足够把它用好呢...下面是在阅读参考文章时候的笔记.

--- 
layout: post 
title: Vector based containers     
categories: programming 
--- 

引言
---- 

# Using STL Vectors
在代码中std::vector经常被用到吧? 因为它很容易用, 只管往里面塞东西就ok了. 
但是用!=用得好, 而对它的了解又是否足够把它用好呢...下面是在阅读参考文章时候的笔记. 

Prefer vector over list 
---- 
比较 std::vector 和 std::list, 其中一个特定是, vector的内存是连续的(contiguous buffers), 还可以避免avoid内存动态分配memory allocations和四散的访问scattered access.  这些都是优势. 

这里发散一下，以前在用Qt 4.8的时候(以后Qt 5可能有变)，就有QVector, QList, QLinkedList，它们的区别也有意思。其中QVector跟std::vector类似, QLinkedList跟std::list类似，而QList的indices是连续的，但是指向的内容又是不连续的. 具体请google.

至于为什么vector的减少内存分配和避免四散访问都能成为优势，背后应该是有更深的理由的，可能跟内存分配有关，我还不懂。

Key issues of vector
---- 
首先一个知识点是, vector.size() != vector.capacity(), 一个表示已经装了多少元素, 另一个表示当前已经分配的空间可以装多少跟元素，有点不同哦。那么当push_back()的时候，size and copacity又是怎么增加的呢? 
```
#include <vector> 
#include <iostream>

int main()
{ 
	std::vector<int> arr; 
	for (int i = 0; i < 15; ++i)
	{
		arr.push_back( i ); 
		std::cout << "size = " << arr.size() << ", capacity = " << arr.capacity() << std::endl;
	}
		
	std::cout << "arr.capacity(): " << arr.capacity() << std::endl; 
	arr.swap( arr ); 
	std::cout << "arr.capacity(): " << arr.capacity() << std::endl;
	std::vector<int>(arr).swap( arr ); 
	std::cout << "arr.capacity(): " << arr.capacity() << std::endl;

	std::getchar();
	return 1; 
};
// the output, from visual studio 2012: 
size = 1, capacity = 1
size = 2, capacity = 2
size = 3, capacity = 3
size = 4, capacity = 4
size = 5, capacity = 6
size = 6, capacity = 6
size = 7, capacity = 9
size = 8, capacity = 9
size = 9, capacity = 9
size = 10, capacity = 13
size = 11, capacity = 13
size = 12, capacity = 13
size = 13, capacity = 13
size = 14, capacity = 19
size = 15, capacity = 19
arr.capacity(): 19
arr.capacity(): 19
arr.capacity(): 15
``` 
看来std::vector可能会预先分配出多余的内存(应该跟compiler的实现有关?)，为什么有这样的策略呢? 减少塞元素时候所需要allocation的次数, 但是当元素减少reduce的时候, 多余的内容就更多了. 
因为会预分配内容，所以可能就会出现over-allocation的问题了.  于是就到swap出场了,  释放多余的内存, 缩小capacity.  这种swap的技术原来有个名字的"shrink-to-fit".  在上面的代码例子中，一开始我还用错了swap方法.

上面的例子还有一个有意思的地方。当要塞入新元素, 没有多余空间了, vector是一下子分配多一点的空间, 这时候capacity > size, 但具体是多多少呢?  我以为是翻一倍，但结果看上去不是，那到底是多少呢？
上面例子中15太小了, 看不出规律，我把它改成150之后，结果就: 
``` 
... 
size = 138, capacity = 141
size = 139, capacity = 141
size = 140, capacity = 141
size = 141, capacity = 141
size = 142, capacity = 211
size = 143, capacity = 211
size = 144, capacity = 211
size = 145, capacity = 211
size = 146, capacity = 211
size = 147, capacity = 211
size = 148, capacity = 211
size = 149, capacity = 211
size = 150, capacity = 211
``` 
211 - 141 = 70，是141的一半! 去看回上面别的数据, 果然:-)  
``` 
	function push_back (new value)：
		if capacity == size
			allocate more memory( size * 0.5f ); 
			// capacity is bigger than size now;
		append the new value;
		++ size;			
``` 

另一个问题是，vector要求内容是连续contiguous的，那当需要allocate more memory的时候，当前内容块的尾巴上却不一定有足够的内容满足要求吧? 这时候就会出现在另一个地方新开辟一个领土, 有足够大的内存来满足要求，把旧地方的值都往新地方去搬，那指向旧值的pointer就失效了吧? 但是假如用int作为index, 那这些index应该还是有效的，值之间的先后次序没有发生改变. 

``` 
这段是题外话。讲到上面index的有效性问题，回想起去年做Mudbox的sculpt layer group时候遇到的undo问题。

-> layer 1 -> layer 2 -> layer 3 -> layer 4;
to delete layer 2: 
	need to record its location before deleting; 
	
undo to insert back the layer 2:
	use the recorded location to insert; 
	
那怎么记录layer 2的location呢? 有几个问题的。用pointer or int index呢？
- pointer的话, 例如记录location = after layer 1. 那假如delete layer 2之后 又delete layer 1, undo to insert back layer 1, undo again to insert back layer 2的时候, 被delete后又被new的layer 1的pointer还是一样吗？
Pointer执行的内存地方是不一样的吧。所以我还是用int index来标识某个layer. 

另外, 用index只是能标识具体某个layer, 但是还需要表示是在这个layer的前面呢，还是在这个layer的后面. 这就特殊了, 当被delete的layer是第一个or最后一个。	
``` 


References: 
---- 
http://upcoder.com/series/1/vectors-and-vector-based-containers/ 作者Thomas Young 是参与开发PathEngine的. 
http://www.codeproject.com/Articles/340797/Number-crunching-Why-you-should-never-ever-EVER-us 
Thank you for sharing your experiences and insights. 