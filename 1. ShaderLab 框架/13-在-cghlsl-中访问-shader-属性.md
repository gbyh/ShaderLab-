## 在Cg / HLSL中访问着色器属性

着色器在属性块中声明材质属性.。如果你想访问一些在着色器程序的性能，你需要有相同的名称声明一个CG / HLSL变量和一个匹配的类型。在着色器教程中提供了一个例子：顶点和片段程序。


例如这些着色器属性:
```
	_MyColor ("Some Color", Color) = (1,1,1,1) 
	_MyVector ("Some Vector", Vector) = (0,0,0,0) 
	_MyFloat ("My float", Float) = 0.5 
	_MyTexture ("Texture", 2D) = "white" {} 
	_MyCubemap ("Cubemap", CUBE) = "" {} 
```

would be declared for access in Cg/HLSL code as:
将宣布访问在Cg / HLSL代码:

fixed4 _MyColor; // low precision type is usually enough for colors
float4 _MyVector;
float _MyFloat; 
sampler2D _MyTexture;
samplerCUBE _MyCubemap;

Cg/HLSL can also accept uniform keyword, but it is not necessary:
Cg / HLSL也可以接受统一的关键字,但没有必要:
uniform float4 _MyColor;

Property types in ShaderLab map to Cg/HLSL variable types this way:
属性类型在ShaderLab映射到Cg / HLSL变量类型：

		Color and Vector properties map to float4, half4 or fixed4 variables.
		Range and Float properties map to float, half or fixed variables.
		Texture properties map to sampler2D variables for regular (2D) textures; Cubemaps map to samplerCUBE; and 3D textures map to sampler3D.

