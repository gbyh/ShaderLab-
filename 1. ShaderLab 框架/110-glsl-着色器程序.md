##GLSL着色器程序

除了使用Cg/HSL着色器程序，OpenGL着色语言（GLSL着色器可以直接写）。

然而,使用原始GLSL只是推荐给测试,或者当你知道你只会针对Mac OS X,OpenGL ES 2.0移动设备或Linux。在正常情况下,Unity将保存Cg / HLSL优化GLSL时必要的。

####GLSL片段

GLSL程序片段被写入在GLSLPROGRAM和ENDGLSL关键字之间。

在GLSL，所有的着色器函数的入口点是一个叫做main()。当Unity加载GLSL着色器，它加载的源次顶点与顶点程序，预处理器定义，并再次为片段与片段程序，预处理器定义。所以单独在GLSL代码段的顶点和片段程序部分的方法是用# ifdef顶点围绕着它们。#判断和# ifdef片段..# endif。每个GLSL代码段必须包含一个顶点程序和一个片段程序。

标准包括文件匹配提供了Cg着色器;他们只是有一个.glslinc扩展:

```csharp
    UnityCG.glslinc
```

顶点着色器的输入来自预定义GLSL变量(gl_Vertex、gl_MultiTexCoord0…)或用户定义的属性。切向量通常只需要一个用户定义的属性:

```csharp
    attribute vec4 Tangent;
```

数据从顶点到片段程序通过不同的变量,例如:

```csharp
varying vec3 lightDir; // vertex shader computes this, fragment shader uses this
```


🔚
