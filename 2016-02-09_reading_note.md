Title: 2016年02月阅读笔记
Date: 2016-02-09 16:20
Modified: 2016-02-09 16:50
Category:    
Tags:  
Slug: 2016年02月阅读笔记  
Authors:


"In fact, there is no need to even enforce that the data be related to 3D", Chapter 2, Coomplete Maya Programming.      
这个话有意思。假设自己来设计一个3d应用程序，会有建模，动画，光照渲染等功能模块，你会怎么解耦和关联各个模块呢，例如某个模块改变了，别的模块需要怎么更新，会不会麻烦。而maya应用了data flow model，数据在不同模块下流入流出，不同模块就是对这些数据来操作。上面那个引入的话，这些流动的data甚至可以不用看作是3d数据。   
例如想到mesh, 什么是mesh? 三维顶点, 用边联系起来，结构halfedge是为了方便访问v/e/f。对mesh操作(sculpt, deform)，是对顶点操作，顶点的数目分布坐标等. 我是首先想到了mesh这个东西，然后把vertex看作是mesh的组成。那现在加入从data flow model的角度来看，是不是可以看作首先是一堆data point, 每一个point刚好三维, 这些data point本身可以描述别的东西，但是这里我们把它们恰好看作是描述一个形状shape, 而这些data point的数据数值在不同模块中流动, 被改变, 它反映的形状也随之改变. mesh只是这些data point的一个外衣.                
例如一堆data point，分裂split出新的data point, 平滑smooth这些point的坐标, 就是细分算法了. 有意思的是data point可以增加, 可以跟旁边别的data point商量组成pattern呢.                         
相比分裂产生新data, mudbox里面的sculpt layer merge, 是几组data point碰头而且合并为一了。 

我上段时间碰到的一个问题，把一个interpolation node独立出来去插值不同的数据，这些数据可以是vertex position, color等。

