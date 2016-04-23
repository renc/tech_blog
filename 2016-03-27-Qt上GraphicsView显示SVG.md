Title: Qt上GraphicsView显示SVG
Date: 2016-03-26 10:20
Modified: 2016-03-26 19:30
Category:   
Tags: reading, 
Slug: Qt上GraphicsView显示SVG  
Authors: 

 
![Screen Shot](svgDemo_screenShot.PNG "output") 

There is a problem came at the daily work, to show a button on a graphics view,                     
this button is supposed to have the hover state, with an icon, but not general image format, but use SVG.  
My first time to use svg image, even i know it is vector image before.                                     
To learn a bit of the Graphics View framework and showing svg, I create this small demo.                                                            

There are some tasks in this demo:                
1. create a scene, add items to it, use view to show the scene;                 
2. load and display svg image, image can be created from online svg editor;                                             
3. add general qt widget to scene, graphicsitem->graphicsobject(signal/slot)                     
   ->graphicswidget(custom widget)->graphicsProxyWidget(use with QWidget).           
4. svg as icon added to a button. by using button, we have the hover state.             

代码在GitHub/coding_exercises/svgDemo               
       
	   