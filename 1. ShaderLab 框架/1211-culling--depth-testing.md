##剔除和深度测试（Culling & Depth Testing）


![](/assets/PipelineCullDepth.png)

背面剔除：剔除是一种优化,不渲染背对观众的多边形。

###语法####1. Cull
```javascript    
    Cull Back | Front | Off
```
控制多边形应该剔除(不绘制)的面。



