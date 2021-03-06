
##Pass Tags

&emsp;&emsp;Pass 使用标签告诉渲染引擎如何及何时希望被渲染。


###语法：

```csharp
    Tags { "TagName1" = "Value1" "TagName2" = "Value2" }
```
&emsp;&emsp;可以有任意多个键值对。

---

####&emsp;&emsp;细节
&emsp;&emsp;标签基本上是 “键“-”值“ 对。Pass 标签被用来控制这个通道的作用，在灯光管道（环境光，顶点光照，像素光照等）和其他一些选项。注意，下面的标签必须在 Pass 部分而不在 SubShader 部分！

---

####&emsp;&emsp;LightMode tag
&emsp;&emsp;LightMode 标签定义了 Pass 在光照管线中的作用。见渲染管道的细节。这些标签很少使用手动；大多数着色器需要与灯光的配合，写成表面着色器，然后照顾所有这些细节。

&emsp;&emsp;**LightMode 标签可以使用的值：**

|值|说明|
|:--|:--|
|Always|总是渲染，无光照。|
|ForwardBase|用于前向渲染，主要应用于平行光、顶点／SH 光照和光照贴图。|
|ForwardAdd|用于前向渲染，用于叠加逐像素光照，每个灯光使用一个 Pass。|
|Deferred|用于延迟渲染， 渲染 g-buffer。|
|ShadowCaster|将物体的深度渲染到深度图中或 shadowmap。|
|PrepassBase|用于 Legacy 的延迟光照，渲染法线和高光指数。|
|PrepassFinal|用于 Legacy 的延迟光照，结合纹理贴图，灯光和自发光渲染最终颜色。|
|Vertex|当物体没有被烘焙，用于 Legacy 的顶点光照渲染；应用于所有的顶点光照。|
|VertexLMRGBM|当物体被烘焙以后，用于 Legacy 的顶点光照渲染；用于光照贴图使用 RGBM 编码的平台上（PC & 控制台）。|
|VertexLM|当物体被烘焙，用于 Legacy 顶点光照渲染，用于光照贴图使用 double-LDR 编码的平台上（移动平台）。|


---

####&emsp;&emsp;RequireOptions tag

只有当外部条件满足的情况下 Pass 才能被执行。这是通过使用requireoptions标签，其值是一个空格分隔的字符串选项。目前，Unity 支持的选项是：

&emsp;&emsp;**SoftVegetation(植被)**: 只有在 `Quality Settings` 中设置 `Soft Vegetaion` 为 `on` 的时候才被渲染。

🔚
