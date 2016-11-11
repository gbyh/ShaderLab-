##Pass Tags

Pass 使用标签告诉渲染引擎如何及何时希望被渲染。


###语法：
---
```javascript
    Tags { "TagName1" = "Value1" "TagName2" = "Value2" }
```
可以有任意多个键值对。

####细节
标签基本上是 “键“-”值“ 对。Pass 标签被用来控制这个通道的作用，在灯光管道（环境光，顶点光照，像素光照等）和其他一些选项。注意，下面的标签必须在 Pass 部分而不在 SubShader 部分！

---

####LightMode tag
LightMode 标签定义了 Pass 在光照管线中的作用。见渲染管道的细节。这些标签很少使用手动；大多数着色器需要与灯光的配合，写成表面着色器，然后照顾所有这些细节。

&emsp;&emsp;**LightMode 标签可以使用的值：**
&emsp;&emsp;Always: 总是渲染，无光照


