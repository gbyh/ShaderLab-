##SubShader Tags

着色器使用Tags来告诉他们如何和何时会被渲染到渲染引擎。

####语法：
```
    Tags { "TagName1" = "Value1" "TagName2" = "Value2" }
```

指定 TagName1 的值为 Value1, TagName2 的值为 Value2。你可以有尽可能多的标签。

####细节：

标签基本上是键值对。在SubShader标签是用来确定SubShader呈现顺序和其他参数。注意，下面的标签被Unity必须SubShader部分，而不是在Pass内部！

In addition to built-in tags recognized by Unity, you can use your own tags and query them using Material.GetTag function.
Unity除了内置标签,你可以使用你自己的标签和使用Material.GetTag函数查询它们。

Rendering Order - Queue tag
You can determine in which order your objects are drawn using the Queue tag. A Shader decides which render queue its objects belong to, this way any Transparent shaders make sure they are drawn after all opaque objects and so on.
您可以确定订单对象的使用队列标记。材质决定渲染队列对象所属,这样任何透明着色器确保他们毕竟绘制不透明的物体等等。

There are four pre-defined render queues, but there can be more queues in between the predefined ones. The predefined queues are:
有四个预定义的渲染队列,但可以有更多的队列在预定义的。预定义的队列:
		Background - this render queue is rendered before any others. You’d typically use this for things that really need to be in the background.
		任何其他人之前呈现这个渲染队列。你通常使用这个东西真的需要在后台。
		Geometry (default) - this is used for most objects. Opaque geometry uses this queue.
		这是用于大多数对象。不透明的几何使用此队列。
		AlphaTest - alpha tested geometry uses this queue. It’s a separate queue from Geometry one since it’s more efficient to render alpha-tested objects after all solid ones are drawn.
		α测试几何使用此队列。从几何的一个一个单独的队列,因为它更高效的渲染alpha-tested物体毕竟固体的。
		Transparent - this render queue is rendered after Geometry and AlphaTest, in back-to-front order. Anything alpha-blended (i.e. shaders that don’t write to depth buffer) should go here (glass, particle effects).
		这个渲染队列呈现几何和AlphaTest后,前后颠倒的顺序。阿尔法混合的东西(即着色器,不要写深度缓冲)应该在这里(玻璃、粒子效果)。
		Overlay - this render queue is meant for overlay effects. Anything rendered last should go here (e.g. lens flares).
		这个渲染队列是用来覆盖效果。任何最后应该呈现(如镜头耀斑)。

Shader "Transparent Queue Example"
{
     SubShader
     {
        Tags { "Queue" = "Transparent" }
        Pass
        {
            // rest of the shader body...
        }
    }
}

An example illustrating how to render something in the transparent queue
一个例子说明如何呈现在透明的队列

For special uses in-between queues can be used. Internally each queue is represented by integer index; Background is 1000, Geometry is 2000, AlphaTest is 2450, Transparent is 3000 and Overlay is 4000. If a shader uses a queue like this:
可以使用特殊用途中间队列。内部每个队列是由整数指数; 
Background是1000, 
Geometry是2000,
AlphaTest是2450, 
Transparent是3000, 
Overlay 4000。
如果一个着色器使用一个队列:

Tags { "Queue" = "Geometry+1" }

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


