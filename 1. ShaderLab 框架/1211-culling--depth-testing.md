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

####&emsp;&emsp;2.1 ZTest
```javascript
        ZTest Less | Greater | LEqual | GEqual | Equal | NotEqual | Always
```
如何进行深度测试。LEqual（默认是绘制对象或在距离为现有对象；隐藏在他们身后的物体）。

####&emsp;&emsp;2.2 Offset
```javascript
        offset Factor, Units
```
允许您指定两个参数的深度偏移量。因素和单位。因子缩放Z的最大斜率，相对于X或Y的多边形，和单位规模的可分辨的最小深度缓冲值。这允许你强制一个多边形被绘制在另一个，虽然他们实际上是在同一位置。例如偏移0，1拉的多边形更接近相机忽略了多边形的斜率，而偏移量的1，1将拉的多边形更接近时，看着一个掠角。

###示例
这个对象只渲染对象的背面:
```javascript
    Shader "Show Insides"
    {
        SubShader
        {
            Pass
            {
                Material
                {
                    Diffuse (1,1,1,1)
                }
                Lighting On
                Cull Front
            }
        }
    }
```
试着把它应用到一个立方体,并注意绕它旋转的时候感觉都错了。这是因为你只看到立方体的内部零件。

###使用深度写的半透明材质

通常半透明阴影不写进深度缓冲。然而,这可以创建画顺序问题,尤其是在复杂的非凸网格。如果你想淡入和淡出网格,然后使用材质填充深度缓冲之前呈现透明可能是有用的。




