[categories] programming, 

刚看到如下代码:
```    
// NvGLModel.h from nvidia samples 
	nv::vec3f m_center; ///< The computed center of the bounding box of the app

private:
	NvModel *model;
	GLuint model_vboID, model_iboID;
	nv::vec3f m_minExtent, m_maxExtent, m_radius;

```           
觉得别扭，主要是有些成员变量data member用来m_，而有些不用，不统一。所谓的taste?
 
btw, 反例的英文原来是: counter example; negative example 
