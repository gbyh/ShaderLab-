## 在Cg / HLSL中访问着色器属性

着色器在属性块中声明材质属性.。如果你想访问一些在着色器程序的性能，你需要有相同的名称声明一个CG / HLSL变量和一个匹配的类型。在着色器教程中提供了一个例子：顶点和片段程序。


例如这些着色器属性:

```csharp
	_MyColor ("Some Color", Color) = (1,1,1,1) 
	_MyVector ("Some Vector", Vector) = (0,0,0,0) 
	_MyFloat ("My float", Float) = 0.5 
	_MyTexture ("Texture", 2D) = "white" {} 
	_MyCubemap ("Cubemap", CUBE) = "" {} 
```

在Cg / HLSL代码中声明:

```csharp
	fixed4 _MyColor; // low precision type is usually enough for colors
	float4 _MyVector;
	float _MyFloat; 
	sampler2D _MyTexture;
	samplerCUBE _MyCubemap;
```

Cg / HLSL也可以接受uniform的关键字,但没有必要:

```csharp
	uniform float4 _MyColor;
```

Property types in ShaderLab map to Cg/HLSL variable types this way:
在ShaderLab的属性类型通过这种方式映射到Cg / HLSL变量类型：

		Color 和 Vector 属性用 float4, half4 或者 fixed4 变量。
		Range 和 Float 属性用 float, half 或者 fixed 变量。
		Texture 属性用 sampler2D 变量; Cubemaps 用 samplerCUBE; 以及 3D 纹理用 sampler3D.

🔚