##剔除和深度测试（Culling & Depth Testing）


![](/assets/PipelineCullDepth.png)

背面剔除：剔除是一种优化,不渲染背对观众的多边形。

###语法
####1. Cull
```javascript    
    Cull Back | Front | Off
```
控制多边形应该剔除(不绘制)的面。


####2. ZWrite
```javascript
    ZWrite On | Off
```
控制像素从这个对象是否写入深度缓冲(默认)。如果你drawng固体物质,离开这。如果你要画半透明效果,切换到ZWrite。

&emsp;&emsp;####2.1 ZTest

