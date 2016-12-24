##Shading Language used in Unity

在Unity中，着色程序是用HLSL语言的一个变种（也叫CG但最实际的使用两者是相同的）。

目前，对于不同的平台之间最大的可移植性，在DX9 HLSL的写作风格（例如使用DX9和纹理采样方式为sampler2D tex2D代替DX10风格Texture2D，samplerstate和tex.sample）。

####Shader Compilers

在内部，不同着色器编译器用于着色器程序编译：

Windows和微软平台（DX9，DX11，DX12和Xbox One）全部使用微软的HLSL编译器（目前d3dcompiler_47）。

OpenGL核心，OpenGL ES 3和金属的使用微软的HLSL其次是字节码翻译成GLSL，使用修改后的版本的hlslcc。

OpenGL ES 2使用源代码级的翻译，通过hlsl2glslfork GLSL优化器。

其他游戏平台使用各自的编译器（例如PSSL PS4）。

表面着色器使用CG 2.2和mojoshader生成代码的分析步骤。

如果你真的需要确定哪些编译器被使用（使用HLSL语法的一个编译器，支持或解决编译错误），预定义的材质可以使用宏。例如，unity_compiler_hlsl设置编译时编译器（对于D3D HLSL或glcore / gles3平台）；和unity_compiler_hlsl2glsl编译时通过hlsl2glsl。


🔚