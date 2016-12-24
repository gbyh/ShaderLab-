##Shading Language used in Unity

在Unity中，着色程序是用HLSL语言的一个变种（也叫CG但最实际的使用两者是相同的）。

Currently, for maximum portability between different platforms, writing in DX9-style HLSL (e.g. use DX9 style sampler2D and tex2D for texture sampling instead of DX10 style Texture2D, SamplerState and tex.Sample).

####Shader Compilers

Internally, different shader compilers are used for shader program compilation:

Windows & Microsoft platforms (DX9, DX11, DX12 and Xbox One) all use Microsoft’s HLSL compiler (currently d3dcompiler_47).
OpenGL Core, OpenGL ES 3 and Metal use Microsoft’s HLSL followed by bytecode translation into GLSL, using a modified version of hlslcc.
OpenGL ES 2.0 uses source level translation via hlsl2glslfork and glsl optimizer.
Other console platforms use their respective compilers (e.g. PSSL on PS4).
Surface Shaders use Cg 2.2 and MojoShader for code generation analysis step.
In case you really need to identify which compiler is being used (to use HLSL syntax only supported by one compiler, or to work around a compiler bug), predefined shader macros can be used. For example, UNITY_COMPILER_HLSL is set when compiling with HLSL compiler (for D3D or GLCore/GLES3 platforms); and UNITY_COMPILER_HLSL2GLSL when compiling via hlsl2glsl.