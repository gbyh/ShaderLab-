##GLSL着色器程序

除了使用Cg/HSL着色器程序，OpenGL着色语言（GLSL着色器可以直接写）。

然而,使用原始GLSL只是推荐给测试,或者当你知道你只会针对Mac OS X,OpenGL ES 2.0移动设备或Linux。在正常情况下,Unity将保存Cg / HLSL优化GLSL时必要的。

####GLSL片段

GLSL program snippets are written between GLSLPROGRAM and ENDGLSL keywords.
GLSL GLSLPROGRAM和编写程序片段ENDGLSL关键词。

In GLSL, all shader function entry points have to be called main(). When Unity loads the GLSL shader, it loads the source once for the vertex program, with the VERTEX preprocessor define, and once more for the fragment program, with the FRAGMENT preprocessor define. So the way to separate vertex and fragment program parts in GLSL snippet is to surround them with #ifdef VERTEX .. #endif and #ifdef FRAGMENT .. #endif. Each GLSL snippet must contain both a vertex program and a fragment program.
在GLSL,所有着色器函数入口点主要有被称为main()。当Unity加载GLSL材质,它加载的源一旦顶点程序,预处理器定义顶点,一旦更多的片段程序,预处理器定义的片段。所以独立的顶点和片段程序部分GLSL片段与# ifdef顶点在他们的周围。# endif和# ifdef片段. .# endif。每个GLSL片段必须同时包含一个顶点程序和一个片段程序。

Standard include files match those provided for Cg shaders; they just have a .glslinc extension:
标准包括文件匹配提供了Cg着色器;他们只是有一个。glslinc扩展:

UnityCG.glslinc

Vertex shader inputs come from predefined GLSL variables (gl_Vertex, gl_MultiTexCoord0, …) or are user defined attributes. Usually only the tangent vector needs a user defined attribute:
顶点着色器的输入来自预定义GLSL变量(gl_Vertex、gl_MultiTexCoord0…)或用户定义的属性。切向量通常只需要一个用户定义的属性:

attribute vec4 Tangent;

Data from vertex to fragment programs is passed through varying variables, for example:
数据从顶点到片段程序通过不同的变量,例如:

varying vec3 lightDir; // vertex shader computes this, fragment shader uses this




