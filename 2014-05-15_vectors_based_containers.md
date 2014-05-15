[categories] programming, 

引言
---- 
> 在代码中std::vector经常被用到吧? 因为它很容易用, 只管往里面塞东西就ok了. 
> 但是用!=用得好, 而对它的了解又是否足够把它用好呢...下面是在阅读参考文章时候的笔记. 

# Using STL Vectors

Prefer vector over list 
---- 
比较 std::vector 和 std::list, 其中一个特定是, vector的内存是连续的(contiguous buffers), 还可以避免avoid内存动态分配memory allocations和四散的访问scattered access.  这些都是优势. 

这里发散一下，以前在用Qt 4.8的时候(以后Qt 5可能有变)，就有QVector, QList, QLinkedList，它们的区别也有意思。其中QVector跟std::vector类似, QLinkedList跟std::list类似，而QList的indices是连续的，但是指向的内容又是不连续的. 具体请google.

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
// the output: 
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






References: 
---- 
http://upcoder.com/series/1/vectors-and-vector-based-containers/ 作者Thomas Young 是参与开发PathEngine的. 
http://www.codeproject.com/Articles/340797/Number-crunching-Why-you-should-never-ever-EVER-us 
Thank you for sharing your experiences and insights. 