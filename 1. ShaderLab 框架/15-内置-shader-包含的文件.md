##内置着色器包含的文件

Unity contains several files that can be used by your shader programs to bring in predefined variables and helper functions. This is done by the standard #include directive, e.g.:
Unity包含多个文件，这些文件可以被着色器程序使用，以提供预定义的变量和辅助函数。这是标准的#include指令，例如：

```
CGPROGRAM
// ...
#include "UnityCG.cginc"
// ...
ENDCG
```

Shader include files in Unity are with .cginc extension, and the built-in ones are:
Unity的着色器包含文件的用.cginc扩展,内置的是:

    HLSLSupport.cginc - (包括自动)跨平台着色器编译的自动宏和定义。
		
    UnityShaderVariables.cginc - (包括自动) 常用的全局变量。
    UnityCG.cginc - 常用的辅助函数。
    AutoLight.cginc - 照明&跟踪功能,如表面着色器在内部使用这个文件。
    Lighting.cginc - 标准的表面材质照明模型;自动包括当你写表面着色器。
    TerrainEngine.cginc - 辅助函数的地形和植被着色器。

HLSLSupport.cginc
这个文件是编译时自动包括着色器。它声明了各种预处理器宏在多平台材质发展援助。

UnityShaderVariables.cginc
这个文件是编译时自动包括着色器。它宣称各种内置的着色器中常用的全局变量。

UnityCG.cginc
这个文件通常是包含在Unity着色器将有许多辅助功能。

在UnityCG.cginc中的结构体：

		struct appdata_base: vertex shader input with position, normal, one texture coordinate.
		struct appdata_tan: vertex shader input with position, normal, tangent, one texture coordinate.
		struct appdata_full: vertex shader input with position, normal, tangent, vertex color and two texture coordinates.
		struct appdata_img: vertex shader input with position and one texture coordinate.

UnityCG.cginc通用的辅助函数

    float3 WorldSpaceViewDir (float4 v) - 返回世界空间方向(非规范化)从给定对象空间顶点位置对着镜头。
    float3 ObjSpaceViewDir (float4 v) - 返回对象空间方向(非规范化)从给定对象空间顶点位置对着镜头。
    float2 ParallaxOffset (half h, half height, half3 viewDir) - 计算UV偏移视差法线贴图。
    fixed Luminance (fixed3 c) - 将颜色转换为亮度(灰度)。
    fixed3 DecodeLightmap (fixed4 color) - 解码颜色从Unity lightmap(RGBM或dLDR取决于平台)。
    float4 EncodeFloatRGBA (float v) - 编码[0 . . 1)浮动范围为RGBA颜色,用于存储在低精度渲染目标。
    float DecodeFloatRGBA (float4 enc) - 解码RGBA颜色到一个浮点数。
    同样,float2 EncodeFloatRG(v)浮动,浮动DecodeFloatRG(float2 enc)使用两个颜色通道。
    float2 EncodeViewNormalStereo (float3 n) - 编码正常视图空间中两个数字0 . .1范围。
		float3 DecodeViewNormalStereo (float4 enc4) - decodes view space normal from enc4.xy.
		从enc4.xy解码视图法线空间。

Forward rendering helper functions in UnityCG.cginc
前向渲染辅助函数在UnityCG.cginc

These functions are only useful when using forward rendering (ForwardBase or ForwardAdd pass types).
这些函数使用向前渲染时只有有用(ForwardBase或ForwardAdd通过类型)。

    float3 WorldSpaceLightDir (float4 v) - 计算世界空间方向(非标准化),给定对象空间顶点位置。
    float3 ObjSpaceLightDir (float4 v) - 计算对象空间方向(非标准化),给定对象空间顶点位置。
    float3 Shade4PointLights (...) - 计算从四个点光源照明,光数据紧密到向量。向前渲染使用这个计算种每个顶点都具备的照明。

在UnityCG.cginc中顶点光照辅助函数

These functions are only useful when using per-vertex lit shaders (“Vertex” pass type).
这些函数只有在逐顶点光照着色器中才有用。

float3 ShadeVertexLights (float4 vertex, float3 normal) - computes illumination from four per-vertex lights and ambient, given object space position & normal.
计算从四种每个顶点都具备的灯光和环境照明,给定对象的空间位置和正常。








