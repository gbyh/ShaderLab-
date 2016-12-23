##传统的 BindChannels

bindchannels命令允许您指定顶点数据映射到图形硬件。

注：当使用顶点程序时，_bindchannels没有效果的，在这种情况下，绑定是由顶点着色器的输入控制。建议使用可编程着色器而不是固定功能顶点处理。

By default, Unity figures out the bindings for you, but in some cases you want custom ones to be used.
默认情况下,统一数据绑定,但在某些情况下,你想要使用自定义的。

For example you could map the primary UV set to be used in the first texture stage and the secondary UV set to be used in the second texture stage; or tell the hardware that vertex colors should be taken into account.
例如可以地图主紫外线组在第一次使用纹理阶段和二级紫外线组中使用第二个纹理阶段;或者告诉硬件顶点颜色应该考虑。

Syntax
BindChannels { Bind "source", target }

Specifies that vertex data source maps to hardware target.
数据源指定顶点映射到硬件的目标。

Source can be one of:
		Vertex: vertex position
		Normal: vertex normal
		Tangent: vertex tangent
		Texcoord: primary UV coordinate
		Texcoord1: secondary UV coordinate
		Color: per-vertex color
Target can be one of:
		Vertex: vertex position
		Normal: vertex normal
		Tangent: vertex tangent
		Texcoord0, Texcoord1, …: texture coordinates for corresponding texture stage
		Texcoord: texture coordinates for all texture stages
		Color: vertex color

Details
Unity places some restrictions on which sources can be mapped to which targets. Source and target must match for Vertex, Normal, Tangent and Color. Texture coordinates from the mesh (Texcoord and Texcoord1) can be mapped into texture coordinate targets (Texcoord for all texture stages, or TexcoordN for a specific stage).
统一的地方一些限制源可以被映射到目标。源和目标必须匹配顶点,法线,切线和颜色。的纹理坐标网格(Texcoord和Texcoord1)可以映射到纹理坐标目标(Texcoord纹理阶段,或TexcoordN为一个特定的阶段)。

There are two typical use cases for BindChannels:
有两个典型的用例BindChannels:

		Shaders that take vertex colors into account.
		Shaders that use two UV sets.

Examples
// Maps the first UV set to the first texture stage
// and the second UV set to the second texture stage
BindChannels {
   Bind "Vertex", vertex
   Bind "texcoord", texcoord0
   Bind "texcoord1", texcoord1
}

// Maps the first UV set to all texture stages
// and uses vertex colors
BindChannels {
   Bind "Vertex", vertex
   Bind "texcoord", texcoord
   Bind "Color", color
}





