##Shading Language used in Unity

在Unity中，着色程序是用HLSL语言的一个变种（也叫CG但最实际的使用两者是相同的）。

目前，对于不同的平台之间最大的可移植性，在DX9 HLSL的写作风格（例如使用DX9和纹理采样方式为sampler2D tex2D代替DX10风格Texture2D，samplerstate和tex.sample）。

####Shader Compilers

在内部，不同着色器编译器用于着色器程序编译：

Windows和微软平台（DX9，DX11，DX12和Xbox One）全部使用微软的HLSL编译器（目前d3dcompiler_47）。

OpenGL核心，OpenGL ES 3和金属的使用微软的HLSL其次是字节码翻译成GLSL，使用修改后的版本的hlslcc。

OpenGL ES 2.0 uses source level translation via hlsl2glslfork and glsl optimizer.
Other console platforms use their respective compilers (e.g. PSSL on PS4).
Surface Shaders use Cg 2.2 and MojoShader for code generation analysis step.
In case you really need to identify which compiler is being used (to use HLSL syntax only supported by one compiler, or to work around a compiler bug), predefined shader macros can be used. For example, UNITY_COMPILER_HLSL is set when compiling with HLSL compiler (for D3D or GLCore/GLES3 platforms); and UNITY_COMPILER_HLSL2GLSL when compiling via hlsl2glsl.