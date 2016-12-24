##SubShader Tags

着色器使用Tags来告诉他们如何和何时会被渲染到渲染引擎。

####语法：
```
    Tags { "TagName1" = "Value1" "TagName2" = "Value2" }
```

指定 TagName1 的值为 Value1, TagName2 的值为 Value2。你可以有尽可能多的标签。

####细节：

标签基本上是键值对。在SubShader标签是用来确定SubShader呈现顺序和其他参数。注意，下面的标签被Unity必须SubShader部分，而不是在Pass内部！

Unity除了内置标签,你可以使用你自己的标签和使用Material.GetTag函数查询它们。


####Rendering Order - Queue tag

可以使用队列标记确定对象的顺序。一个着色器决定对象属于的渲染队列，这样任何透明着色器确保他们在不透明的物体之后被绘制等等。

以下是预定义的渲染队列，但可以有更多的队列之间的预定义的。预定义的队列是：

|队列|说明|
|:--|:--|
|Background|此渲染队列在任何其他人之前呈现。|
|Geometry (default)|这是用于大多数对象。不透明的几何体使用此队列。|
|AlphaTest|alpha test的几何结构使用此队列。这是一个单独的队列从几何之一，因为它是更有效地渲染alpha test对象后，所有不透明的绘制。|
|Transparent|该渲染队列渲染几何和AlphaTest后，从后到前的顺序。什么alpha混合（即着色器，不写入深度缓冲）应该去这里（玻璃、粒子效果）。|
|Overlay|此渲染队列是用于覆盖效果。最后渲染的任何东西都应该放在这里（例如镜头光晕）|
		 
```
Shader "Transparent Queue Example"
{
     SubShader
     {
        Tags { "Queue" = "Transparent" } // 如何在透明队列中呈现

        Pass
        {
            // rest of the shader body...
        }
    }
}
```

每个队列是由整数指数为代表：

    Background 是 1000, 
    Geometry 是 2000,
    AlphaTest 是 2450, 
    Transparent 是 3000, 
    Overlay 4000。
    
如果一个着色器使用一个队列:
```
    Tags { "Queue" = "Geometry+1" }
```

这将使对象在所有不透明对象之后呈现，但在透明对象之前，呈现队列索引将是2001（几何加1）。这是非常有用的情况下，你希望一些对象总是在其他对象集之间绘制。例如，在大多数情况下，透明的水应该绘制在不透明的物体，但在透明物体之前。

队列2500（“Geometry + 500”）被认为“不透明”和优化为最佳性能对象的绘制顺序。更高的渲染队列被认为是“透明对象”和对象根据距离排序，开始渲染从最远到最接近的。天空盒被绘制在所有不透明和透明物体之间。

####RenderType tag

RenderType标签分类着色器分成几个预定义组,例如,是一个不透明的材质,或一个alpha-tested材质等。这是材质替换和在某些情况下使用的用于生产相机的深度纹理。

####DisableBatching tag

当Draw Call Batching被使用的时候一些着色器不工作，那是因为批处理转换所有的多边形到世界空间，所以物体空间丢失。

DisableBatching标记可用于incidate。有三个可能的值：“True”(总是禁用批处理这种材质)，“False”(不会禁用批处理；这是缺省值)和“LODFading”(LOD衰落时禁用批处理活动，主要是用于树)。

####ForceNoShadowCasting tag

如果ForceNoShadowCasting标记的值为“True”，然后使用这个subshader呈现的对象永远不会阴影。这是很有用的，当你使用材质替换透明的对象和你不会从另一个subshader继承一个影子。

####IgnoreProjector tag

如果IgnoreProjector标记的值为“True”，然后一个对象，使用这种材质投影仪将不受影响。这主要是有用的半透明的对象，因为没有投影仪有影响他们的好方法。

####CanUseSpriteAtlas tag

CanUseSpriteAtlas标记设置为“False”，如果材质是精灵,当他们在图集中是无用的(见Sprite Packer)。

####PreviewType tag

PreviewType表明material在监视面板预览应该显示material。默认情况下显示材质球，但PreviewType也可以设置为“Plane”(将显示为2d)或“Skybox”(将显示天空体)。

####See Also
Passes 也可以设置 Tags, 见 Pass Tags.

🔚
