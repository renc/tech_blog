Title: Algo - Tower of Hanoi 
Date: 2014-11-28 20:20   
Modified: 2014-11-28 20:30    
Category: programming   
Tags: Algo,    
Slug: Algo - Tower of Hanoi    
Authors:    
    
在书[Anany Levitin, 算法设计与分析基础]的第二章又看到汉诺塔的问题:     
有三个柱子A B C; A柱上有好几个盘子, 小的放在大的上面;     
现在问怎么把A柱上的盘子移到C柱上, B是做辅助的, 条件是大的盘子不能压到小的盘子上. 所以结果C柱上的盘子也是小的放在大的上面。    
    
A,B,C; 三个柱子     
    
假如A上有1个盘子，直接放到C上就解答完了。    
    
假如A上有2个盘子ab，a盘小的 在 b盘大的上面. 先把a放到B上, b放到C上, 然后把a从B移到C上. 结果就是C柱上有ab。[pattern 1]     
注意这种case中先把a放到C上, 那b放到B上, 然后a从C移到到B柱上, 那B柱上就有ab两个盘子了. 这结果很像上面的, 但是B柱跟C柱次序反了. 可见在移动盘子的时候, 柱子的次序, 是很重要的.   
     
假如A上有3个盘子, 去尝试多画几次, 还是能解出来, 但是发现好像没有什么规律啊.     
假如A上有4个盘子, 那可能就要尝试好多次才能碰巧一次解答出来. 还没有看出来规律.   
          
要换一种角度来看啊, 上面我们已经解答了A柱上有2个盘子的case了. 那重新看A柱上有3个盘子的case.          
        
A柱上的2个盘子 能借助B移到到C上, 那换个次序, 也能实现: 把A柱上的2个盘子, 借助C移动到B上.[pattern 2]     
          
那假如A柱上有3个盘子abc, 那先把最上面的两个ab按着[pattern 2]移动到B上, 然后把A柱上留下来的c移动到C. 结果是A[0], B[ab], C[c]. 其中C[c]就可以是最终结果的一部分了, 已经移好了. 余下的B[ab]再按着[pattern 2]的模式, 借助A移动到C上. 就解决了整个问题.           
      
那假如A柱上有4个盘子abcd, 先看最上面的abc3个盘子,(用了上面的移动3个盘子的方法)  那先把上面的abc3个盘子借助C移动到B, 然后把d移动到C,          
A[0], B[abc], C[d];          
然后把B上的3个盘子也用上面的方法, 借助A, 移动到C上. 就完整解决这case了.   
          
解决n个盘子的case, 可以用解决n-1个盘子的case来做!        
             
也就是A[1 to n], B[0], C[0]的情况可以通过:         
A[n], B[1, n-1], C[0], A借助C 把盘子移动到B;         
A[0], B[1, n-1], C[n], A上的最后一个盘子移到C;          
A[0], B[0], C[1 to n], B借助A 把盘子移动到C.        
   
```   
// move n elements from A to C, using B
void hanoi(A, n, B, C)    
{       
    hanoi(A, n-1, C, B); // from A to B, using C;          
    move A.last to C;        
    hanoi(B, n-1, A, C); // from B to C, using A;           
}   
```   
这是递归算法. 

书中就是以这算法例子来讲怎么算它的时间复杂度, time complexity. 根据书中介绍的analysis framework:               
input size: n;         
basic operation: move element, bigTheta(1);       
recursive relation: 递归关系式, T(n) = T(n-1) + 1 + T(n-1) = 2*T(n-1) + 1; 所以时间复杂度基本上是2^n级别的(order of grow).    
         
         
