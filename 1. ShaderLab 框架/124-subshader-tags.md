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

Rendering Order - Queue tag

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
This will make the object be rendered after all opaque objects, but before transparent objects, as render queue index will be 2001 (geometry plus one). This is useful in situations where you want some objects be always drawn between other sets of objects. For example, in most cases transparent water should be drawn after opaque objects but before transparent objects.
毕竟这将使物体呈现不透明的物体,但是在透明物体之前,渲染队列指数将达到2001(几何+ 1)。这是有用的情况下,你想要一些对象之间总是吸引其他的对象集。例如,在大多数情况下,透明的水后应绘制不透明的物体,但之前透明的对象。

Queues up to 2500 (“Geometry+500”) are consided “opaque” and optimize the drawing order of the objects for best performance. Higher rendering queues are considered for “transparent objects” and sort objects by distance, starting rendering from the furthest ones and ending with the closest ones. Skyboxes are drawn in between all opaque and all transparent objects.
队列2500(“Geometry + 500”)认为“不透明”的绘制顺序和优化对象的最佳性能。高渲染队列被认为是“透明物体”,对象的距离,开始从最远的呈现,以最接近的人。包厢之间在所有不透明和透明的对象。

RenderType tag
RenderType tag categorizes shaders into several predefined groups, e.g. is is an opaque shader, or an alpha-tested shader etc. This is used by Shader Replacement and in some cases used to produce camera’s depth texture.
RenderType标签分类着色器分成几个预定义组,例如,是一个不透明的材质,或一个alpha-tested材质等。这是材质替换和在某些情况下使用的用于生产相机的深度纹理。

DisableBatching tag
Some shaders (mostly ones that do object-space vertex deformations) do not work when Draw Call Batching is used – that’s because batching transforms all geometry into world space, so “object space” is lost.
当Draw Call Batching被使用的时候一些着色器不工作，那是因为批处理转换所有的多边形到世界空间，所以物体空间丢失。

DisableBatching tag can be used to incidate that. There are three possible values: “True” (always disables batching for this shader), “False” (does not disable batching; this is default) and “LODFading” (disable batching when LOD fading is active; mostly used on trees).
DisableBatching标记可用于incidate。有三个可能的值:“真正的”(总是禁用批处理这种材质),“假”(不会禁用批处理;这是缺省值)和“LODFading”(LOD衰落时禁用批处理活动,主要是用于树)。

ForceNoShadowCasting tag
If ForceNoShadowCasting tag is given and has a value of “True”, then an object that is rendered using this subshader will never cast shadows. This is mostly useful when you are using shader replacement on transparent objects and you do not wont to inherit a shadow pass from another subshader.
如果ForceNoShadowCasting标记的值为“True”,然后使用这个subshader呈现的对象永远不会阴影。这是很有用的,当你使用材质替换透明的对象和你不不会继承一个影子从另一个subshader。

IgnoreProjector tag
If IgnoreProjector tag is given and has a value of “True”, then an object that uses this shader will not be affected by Projectors. This is mostly useful on semitransparent objects, because there is no good way for Projectors to affect them.
如果IgnoreProjector标记的值为“True”,然后一个对象,使用这种材质投影仪将不受影响。这主要是有用的半透明的对象,因为没有投影仪影响他们的好方法。

CanUseSpriteAtlas tag
Set CanUseSpriteAtlas tag to “False” if the shader is meant for sprites, and will not work when they are packed into atlases (see Sprite Packer).
CanUseSpriteAtlas标记设置为“False”,如果材质是精灵,不工作时挤在地图册(见雪碧封隔器)。

PreviewType tag
PreviewType indicates how the material inspector preview should display the material. By default materials are displayed as spheres, but PreviewType can also be set to “Plane” (will display as 2D) or “Skybox” (will display as skybox).
PreviewType表明材料检查员预览应该显示材料。默认情况下显示材料领域,但PreviewType也可以设置为“Plane”(将显示为2 d)或“Skybox”(将显示天空体)。

See Also
Passes can be given Tags as well, see Pass Tags.


