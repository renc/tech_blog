Title: texFormat的一次bugFix
Date: 2015-08-13 20:20
Modified: 2015-08-13 20:30
Category:   
Tags:  
Slug: texFormat的一次bugFix  
Authors:

故事的开头是有个bug说map extraction的ptex文件file1, 在import as paint layer and export paint layer again之后文件file2的大小相差几倍，怀疑file1跟file2中pixel的value不一样了。             
file1.ptex  vs. file2.ptex                                         
                                    
用ptex代码附带的ptexInfo程序把ptex file的data dump出来, 默认是dump all faces, all levels, 发现数据量太大了。 需要ptexInfo做些修改, 只把个别感兴趣的face的数据输出.      
花时间看ptex代码， ptex所调用的zlib可以把压缩关了, 另外ptex还可以关了generate mipmap， 这样ptex写出的数据应该就是没有压缩的而且只有level0的. 所谓的level0就是full resolution.       
                   
发现虽然file1.f0, file1.f0, 两者的resolution, pixel size都是一样的, 但是pixel value还是不一样, file1.f0的每一个pixel的RGBA value每一个channel都相对小了0.003左右.                               
                  
继续追踪, 发现ptex import as layer之后pixel value还是一样的, 而是后面的某个操作中把layer的texture从GPU放到CPU, 然后再从CPU到GPU时候pixel却不一样了. 最后发现是默认建texture的函数glTexImage2D用的是32 internal format, 而后面从CPU到GPU那时候建texture调用glTexImage2D竟然用了16 internal format.  				  
           
            
