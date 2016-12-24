## 在Cg / HLSL中访问着色器属性

Shader declares Material properties in a Properties block. If you want to access some of those properties in a shader program, you need to declare a Cg/HLSL variable with the same name and a matching type. An example is provided in Shader Tutorial: Vertex and Fragment Programs.
着色器声明材料属性在属性块。如果你想访问这些属性在着色器程序中,您需要声明一个Cg / HLSL具有相同名称的变量和一个匹配的类型。着色器教程中提供了一个例子:顶点和片段程序。

For example these shader properties:
例如这些材质属性:

_MyColor ("Some Color", Color) = (1,1,1,1) 
_MyVector ("Some Vector", Vector) = (0,0,0,0) 
_MyFloat ("My float", Float) = 0.5 
_MyTexture ("Texture", 2D) = "white" {} 
_MyCubemap ("Cubemap", CUBE) = "" {} 

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

