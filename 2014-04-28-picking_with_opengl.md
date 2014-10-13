Title: Picking with OpenGL  
Date: 2014-04-28 10:20
Modified: 2014-04-28 19:30
Category: programming 
Tags: 
Slug: Picking with OpenGL 
Authors: 
 

> If you have choices, choose the best. If you have no choice, do the best. 如果有选择，那就选择最好的；如果没有选择，那就努力做到最好。

# Picking with OpenGL

在3d软件提供交互的时候，例如先选择了某个vertex或者某个face，然后去操作这个被选中的东西。如在maya里面很多工具(maya里面把context==tool?)都是先pick the manipulator, drag the manipulator, to update the attributes of the node (the selection list). 第一步就是要选中mouse底下的东西，这是怎么做到的呢?

其实一开始我想到的方法ray intersection的方法，
approach 0. check the intersection between a ray and object.
但是看到貌似下面这两种方面更常用:
approach 1. use the select mode in opengl;
approach 2. color coding/picking: render objects in different color, pick a color, map to the object. 

## Approach 0. ray intersection. 
Reference example: cinder, version 0.8.5, samples/Picking3D. 
(这个cinder的例子另一有意思地方是它对mouse event的响应respond是模拟simulate maya的. 具体在MayaCamUI.h里面, 根据mouse event来判断是在panning/tumbling/zooming哪个模式下, 然后更新perspective camera的position等信息)

假如是一个ray跟mesh上triangle faces做测试. 
  Ray = point + vector direction(end point - start point);
  Triangle face = (point0, point1, point2); 
Question: 怎么求出ray? 得到的坐标是在world space, camera space, or model's object space ? 
                                           
## Approach 1. using selection buffer 
http://content.gpwiki.org/index.php/OpenGL:Tutorials:Picking



## Approach 2. color picking

上面的approach 1有什么问题呢? 
http://www.opengl.org/wiki/Common_Mistakes#Selection_and_Picking_and_Feedback_Mode 
貌似执行在CPU端，在某些实现上性能不行performance is lousy(新单词:-). 于是, 在modern opengl中被deprecated and even removed了，取而代之的是这approach 2里面的color picking方法. 引用自上文: A modern OpenGL program should do color picking (render each object with some unique color and glReadPixels to find out what object your mouse was on) or do the picking with some 3rd party mathematics library (我怀疑这里的math方法就是指ray-intersection方法).

TODO...
