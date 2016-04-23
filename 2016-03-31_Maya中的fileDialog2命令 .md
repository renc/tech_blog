Title: Maya中的fileDialog2命令 
Date: 2016-03-31 21:30
Modified: 2016-03-31 21:30
Category:   
Tags:  
Slug: Maya中的fileDialog2命令 
Authors: 
             
今天在使用maya的fileDialog2 cmd去建一个file dialog, 要求能选中打开多个files, 于是需要使用:                
fileDialog2 -fileMode 4 ….;                                          
这里-fileMode=4 表示支持多个文件选择.                 
问题是执行这个file dialog的时候，右下角两个按钮分别是Save, Cancel. 而我需要是Open, Cancel.     
                    
debug时候发现原来是对同一个地方分别两次操作，而产生冲突.                        
setAcceptMode(QFileDialog::AcceptOpen); // 影响open or save 这个按钮上的字.             
setLebelText(QFileDialog::Accept, "Save"); 也是影响用一个地方，但是这个"Save"跟上面的AcceptOpen中默认使用的"Open"就冲突了. 这个函数允许随意改这个角色上的字.                            
       
解决方法是使用-okc "Open" flag来明确设置这个accept button上的text.              
 
References: Qt4.8 (or Qt5.6), QFileDialog. 
