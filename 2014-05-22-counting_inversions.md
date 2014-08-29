Title: algo - counting inversions
Date: 2014-05-22 10:20
Modified: 2014-05-22 19:30
Category: programming 
Tags: 
Slug: algo - counting inversions
Authors: 
Summary: 算法练习: counting inversions

--- 
layout: post 
title: algo - counting inversions 
categories: programming  
--- 

Background
---- 
Given an array, say [1, 3, 5, 2, 4, 6], to count the inversions.
What is an inversion ?
if (i < j) but arr[i] > arr[j]. 例如上面的(3, 2), 3在2的前面, 但是3>2, 构成一个inversion. 这个例子中一共三个inversions(3, 2) (5, 2) (5, 4). 
                                              
这个计算有什么用呢? 我是[Algorithms: Design and Analysis, Part 1, at coursera.org]中学到的. 
例如你在某个网站上看了六部电影，分别是[1, 2, 3, 4, 5, 6]，然后你给他们按喜好排序，得到数组[1, 3, 5, 2, 4, 6] . 那么求出你这个数组的inversions == 3. 
而在网站的数据库中，找到另一个人他也看了这六部电影并按喜好排序了，那再求出他的数组的inversions, 假如你们的inversion一样，那是否可以猜测你们的喜好一样，那他喜欢看的电影你也可能喜欢，于是网站就给你推荐他看作并mark为喜好的电影好了。

inversions表示了相似度... 这个有意思.


The Algorithms
----                                         
那到底怎么计算呢? 根据定义，当然可能用brute-force来啦，虽然人家可能觉得很傻很笨，但这方法却有一个好处：简单，容易实现。例如后面讲到的方法虽然快，好像很高深，但是在实现中会容易出错，不是算法错了，是写代码含bug了，都不知道是否实现对了，这时候，把结果跟brute-fouce的结果比较一下就知道了...
课上介绍是用divide-and-conquer的策略来做的，利用了merge-sort中merge时候的一个特点，刚好可以求inversions. 
``` 
iCount += arrL.size() - i; 
``` 
我的实现在:  https://github.com/renc/coding_exercises/tree/master/InversionCounter                               

在实现中体会到的:  
- 假如左右两个小数组都已经升序(整个数组都升序), 那么没有inversion, inversion = 0. 
- 假如左右两个小数组都逆序(整个数组都逆序), 那么inversions就最大. 对于1->n组成的数组, max_inversion = (1 + (n-1))(n-1) / 2 = (n-1)n/2;  例如[7, 6, 5, 4, 3, 2, 1]的inversions=6+5+4+3+2+1=21. 

