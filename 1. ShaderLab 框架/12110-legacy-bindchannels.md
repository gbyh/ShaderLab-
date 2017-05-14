---
##传统的 BindChannels

&emsp;&emsp;bindchannels命令允许您指定顶点数据映射到图形硬件。

>注：当使用顶点程序时，_bindchannels没有效果的，在这种情况下，绑定是由顶点着色器的输入控制。建议使用可编程着色器而不是固定功能顶点处理。

&emsp;&emsp;默认情况下，Unity为你绘制绑定，但在某些情况下，你希望使用自定义绑定。

---
例如，你可以在第一个纹理阶段和在第二个纹理阶段使用的二次UV集绘制主UV集，或者告诉硬件顶点颜色应该被考虑在内。

####&emsp;&emsp;语法：
```
		BindChannels { Bind "source", target }
```

指定顶点数据源映射到硬件目标。
```
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
```

####细节：
Unity放置一些限制，哪些源可以映射到哪个目标。源和目标必须匹配顶点，法线，切线和颜色。纹理坐标网格（Texcoord和Texcoord1）可以映射到纹理坐标的目标（Texcoord所有纹理阶段，或texcoordn为一个特定的阶段）。

BindChannels有两个典型的用例：

		Shaders that take vertex colors into account.
		Shaders that use two UV sets.

Examples

```
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
```


🔚

