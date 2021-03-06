Title: mesh history 
Date: 2014-05-28 10:20
Modified: 2014-05-28 19:30
Category:  
Tags: maya 
Slug: mesh history in maya 
Authors: 

做demo都只是对mesh做一次修改, 但是好像在一个成熟的程序里面往往是很多次修改的叠加. 

引言
---- 
以前对mesh的修改都是例如
mesh.setVertexPosition( vertexID, newPos ); 
mesh.setVertexTC( tcID, newTC ); 
这样直接改[1], 从读书时候到现在都是这么simple and naive:-) 而对于maya的case就没有这样简单了，例如去看maya>devkit>splitUV example [2],  套用上面直接修改的方法, 要split一个uv的话，那直接用MFnMesh加几个新uv, MFnMesh::setUV(new uv id, uv value) 就ok了, 可是故事没有这么简单.... 


Construction History and Tweak 
---- 
直接摘抄两段来看看:
> All the operations that can be performed on a polygonal mesh can be generalized into three basic types: create, edit and query. While both the creating and querying a mesh are straightforward, editing involves complications resulting from construction history and tweaks and how they work within the confines of the dependency graph. (from [3])
> There are many intricacies involving modifying a polygonal mesh, including construction history and tweaks. If the mesh does not have history, you could attempt to unshare the UVs directly on the mesh itself. If the mesh has history, any DG evaluation from a node upstream in the construction history overwrites the mesh on the mesh node and the modifications made directly to the mesh would be lost. Even if that were the case, the existence of tweaks would change the appropriate place to write the modifications on the mesh. (from [2])

先看construction history这概念. 

幻想一下，对一个mesh做sculpting, 先假设sculpt的对象是vertices positions, 
new pos  of vertex Vi = stroke1 + stroke 2 + ... stroke n (with some specified brush properties) effect on vertex Vi;   
其中 stroke j effect on vertex Vi: 
case 1. = 0, if this stroke does not touch the vertex Vi; 
case 2. = a delta vector (a displacement); 
也就是说当前的顶点vi的坐标是由一连串chain的stroke operation来决定的, 这一串operation就形成了一个history. 假如允许回到过去, 修改某个stroke i, 那么这个历史被改变了之后, 当前顶点vi的坐标又不同了. (这不是电影桥段吗?:-)

Maya里面的construction history可能也是类似，只是不单单影响vertex position.
                                                                                                         
                                                                                                           
那tweak node又是干啥的？

暂时的理解不一定对和全啊：maya不是有DG和construction history吗，例如一个shape node处于dg的下游，上游的别的其它node会不断改变，mark为dirty，然后当需要的时候，例如渲染的时候，就从DG的上游到下游来从新计算被dirty影响的node，包括shape node。也就是说，shape node只是人家揉捏的结果，人家改了，shape node也得变。所以假如你想直接改shape的形状，那是没有意义的，因为你改了，上游人家一改，就会重新冲掉你的改动。怎么解决呢，引入tweak node。在shape node的直接上游，+一个tweak node作为过滤，不论你们上游怎么改，都要经过我这个tweak node的过滤，因为我下面直接就是shape node嘛，所以你们上面怎么搞最后还不是到我这里。一妇当关万夫莫过。



题外话 
---- 
技术可能有三个层次：算法, 架构, 业务知识. 这是SQ.B在今年performance review时候教我的, 很有意思。回顾之前的Muxxxx, 
API  + core (framework) + plugin  + exe 
的结构，在做一个feature的时候,  尽量放到plugin里面去做, 不影响core, 可能要添加api. 这方面都比较直观。
然后跟着就是node+attribute system, brush system, undo/redo operation stack等....
然后就是在这个框架的布局下, 某个具体算法或者功能的实现。

但是在maya这边架构就复杂很多了，例如就算是一个简单的功能，都要考虑到DG, construction history, tweak node等. 还有meta data等概念等着去学习呢. 


References
---- 
[1] mudbox sdk;
[2] maya > devkit plugin > splitUVCmd example; 
[3] maya developer help > Polygon API > Construction History and Tweaks; 其实maya developer help > Polygon API这一节都看吧.
[4] maya Developer Resources > API Guide > Polygon API > How polygons are handled internally 这算是[3]的基础. 
 