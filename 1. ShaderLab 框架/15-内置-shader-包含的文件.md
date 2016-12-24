##内置着色器包含的文件

Unity contains several files that can be used by your shader programs to bring in predefined variables and helper functions. This is done by the standard #include directive, e.g.:
Unity包含多个文件,可以使用着色器程序将预定义的变量和辅助函数。这是通过标准的# include指令,例如:

CGPROGRAM
// ...
#include "UnityCG.cginc"
// ...
ENDCG

Shader include files in Unity are with .cginc extension, and the built-in ones are:
Unity的材质包含文件。cginc扩展,内置的是:

		HLSLSupport.cginc - (automatically included) Helper macros and definitions for cross-platform shader compilation.
		(包括自动)助手为跨平台的着色器编译宏和定义。
		UnityShaderVariables.cginc - (automatically included) Commonly used global variables.
		(包括自动)常用的全局变量。
		UnityCG.cginc - commonly used helper functions.
		常用的辅助函数。
		AutoLight.cginc - lighting & shadowing functionality, e.g. surface shaders use this file internally.
		照明&跟踪功能,如表面着色器在内部使用这个文件。
		Lighting.cginc - standard surface shader lighting models; automatically included when you’re writing surface shaders.
		标准的表面材质照明模型;自动包括当你写表面着色器。
		TerrainEngine.cginc - helper functions for Terrain & Vegetation shaders.
		TerrainEngine。cginc——辅助函数的地形和植被着色器。

These files are found inside Unity application ({unity install path}/Data/CGIncludes/UnityCG.cginc on Windows,
这些文件在里面Unity应用程序({统一安装路径} /数据/ CGIncludes / UnityCG。cginc在Windows上,
/Applications/Unity/Unity.app/Contents/CGIncludes/UnityCG.cginc on Mac), if you want to take a look at what exactly is done in any of the helper code.
如果你想看看究竟是什么做的辅助代码。

HLSLSupport.cginc
This file is automatically included when compiling shaders. It declares various preprocessor macros to aid in multi-platform shader development.
这个文件是编译时自动包括着色器。它声明了各种预处理器宏在多平台材质发展援助。

UnityShaderVariables.cginc
This file is automatically included when compiling shaders. It declares various built-in global variables that are commonly used in shaders.
这个文件是编译时自动包括着色器。它宣称各种内置的着色器中常用的全局变量。

UnityCG.cginc
This file is often included in Unity shaders to bring in many helper functions.
这个文件通常是包含在统一着色器将在许多辅助功能。

Data structures in UnityCG.cginc

		struct appdata_base: vertex shader input with position, normal, one texture coordinate.
		struct appdata_tan: vertex shader input with position, normal, tangent, one texture coordinate.
		struct appdata_full: vertex shader input with position, normal, tangent, vertex color and two texture coordinates.
		struct appdata_img: vertex shader input with position and one texture coordinate.

Generic helper functions in UnityCG.cginc
通用在UnityCG.cginc辅助函数

		float3 WorldSpaceViewDir (float4 v) - returns world space direction (not normalized) from given object space vertex position towards the camera.
		返回世界空间方向(非规范化)从给定对象空间顶点位置对着镜头。
		float3 ObjSpaceViewDir (float4 v) - returns object space direction (not normalized) from given object space vertex position towards the camera.
		返回对象空间方向(非规范化)从给定对象空间顶点位置对着镜头。
		float2 ParallaxOffset (half h, half height, half3 viewDir) - calculates UV offset for parallax normal mapping.
		计算UV偏移视差法线贴图。
		fixed Luminance (fixed3 c) - converts color to luminance (grayscale).
		将颜色转换为亮度(灰度)。
		fixed3 DecodeLightmap (fixed4 color) - decodes color from Unity lightmap (RGBM or dLDR depending on platform).
		解码颜色从Unity lightmap(RGBM或dLDR取决于平台)。
		float4 EncodeFloatRGBA (float v) - encodes [0..1) range float into RGBA color, for storage in low precision render target.
		编码[0 . . 1)浮动范围为RGBA颜色,用于存储在低精度渲染目标。
		float DecodeFloatRGBA (float4 enc) - decodes RGBA color into a float.
		解码RGBA颜色到一个浮点数。
		Similarly, float2 EncodeFloatRG (float v) and float DecodeFloatRG (float2 enc) that use two color channels.
		同样,float2 EncodeFloatRG(v)浮动,浮动DecodeFloatRG(float2 enc)使用两个颜色通道。
		float2 EncodeViewNormalStereo (float3 n) - encodes view space normal into two numbers in 0..1 range.
		编码正常视图空间中两个数字0 . .1范围。
		float3 DecodeViewNormalStereo (float4 enc4) - decodes view space normal from enc4.xy.
		从enc4.xy解码视图法线空间。

Forward rendering helper functions in UnityCG.cginc
前向渲染辅助函数在UnityCG.cginc

These functions are only useful when using forward rendering (ForwardBase or ForwardAdd pass types).
这些函数使用向前渲染时只有有用(ForwardBase或ForwardAdd通过类型)。

		float3 WorldSpaceLightDir (float4 v) - computes world space direction (not normalized) to light, given object space vertex position.
		计算世界空间方向(非标准化),给定对象空间顶点位置。
		float3 ObjSpaceLightDir (float4 v) - computes object space direction (not normalized) to light, given object space vertex position.
		计算对象空间方向(非标准化),给定对象空间顶点位置。
		float3 Shade4PointLights (...) - computes illumination from four point lights, with light data tightly packed into vectors. Forward rendering uses this to compute per-vertex lighting.
		计算从四个点光源照明,光数据紧密到向量。向前渲染使用这个计算种每个顶点都具备的照明。

Vertex-lit helper functions in UnityCG.cginc
顶点光照辅助函数在UnityCG.cginc中

These functions are only useful when using per-vertex lit shaders (“Vertex” pass type).
这些函数只有在逐顶点光照着色器中才有用。

float3 ShadeVertexLights (float4 vertex, float3 normal) - computes illumination from four per-vertex lights and ambient, given object space position & normal.
计算从四种每个顶点都具备的灯光和环境照明,给定对象的空间位置和正常。








