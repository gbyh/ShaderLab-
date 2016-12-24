##着色器语义

####顶点着色器输入语义

主要的顶点着色器函数（由# pragma指令表示顶点）需要在所有的输入参数的语义。这些对应于单独的网格数据元素，如顶点位置，法线网格，纹理坐标。查看更多细节的顶点程序输入。

这里有一个简单的顶点着色器，它以顶点位置和纹理坐标作为输入。像素着色器将纹理坐标作为一种颜色。
```javascript
    Shader "Unlit/Show UVs"
    {
        SubShader
        {
            Pass
            {
                CGPROGRAM
                #pragma vertex vert
                #pragma fragment frag
    
                struct v2f {
                    float2 uv : TEXCOORD0;
                    float4 pos : SV_POSITION;
                };
    
                v2f vert (
                    float4 vertex : POSITION, // vertex position input
                    float2 uv : TEXCOORD0 // first texture coordinate input
                    )
                {
                    v2f o;
                    o.pos = UnityObjectToClipPos(vertex);
                    o.uv = uv;
                    return o;
                }
                
                fixed4 frag (v2f i) : SV_Target
                {
                    return fixed4(i.uv, 0, 0);
                }
                ENDCG
            }
        }
    }
```

![](/assets/SemanticsShowUVs.png)


####片段着色器输出语义
最常见的一个片段（像素着色器输出色彩，）有sv_target语义。上面例子中的片段着色器确实是这样的：

```javascript
    fixed4 frag (v2f i) : SV_Target
```

也可以返回具有输出的结构.。上面的片段着色器也可以这样重写，它会完全一样：

```javascript
    struct fragOutput {
        fixed4 color : SV_Target;
    };            
    fragOutput frag (v2f i)
    {
        fragOutput o;
        o.color = fixed4(i.uv, 0, 0);
        return o;
    }
```

从片段着色器返回结构主要是用于着色，不只是返回一个单一的颜色。由片段着色器输出支持的附加语义如下。

####SV_TargetN: Multiple render targets
SV_Target1，SV_Target2，等：这些额外的颜色由着色器编写。这是用在渲染成多个渲染目标一次（称为多个渲染目标的渲染技术，或MRT）。SV_Target0是SV_Target相同。

####SV_Depth: Pixel shader depth output

通常的片段着色器不重写Z缓冲区的值，和一个默认的值是从规则的三角形光栅化用。然而，对于某些影响，它对输出每像素的自定义Z缓冲区深度值是有用的。

多GPU这让一些深度缓存优化，注，所以不要重写Z缓冲区的值没有一个好的理由。SV_Depth招致的成本取决于GPU架构，但总的说来是相当类似的α测试的成本（使用HLSL内置clip()功能）。渲染着色器，修改深度毕竟规则不透明的着色器（例如，通过使用透明度测试渲染队列。

输出的深度值必须被定义为float类型的。


####Vertex shader outputs and fragment shader inputs

顶点着色器需要输出最终的剪辑空间位置的一个顶点，使GPU知道屏幕上的栅格化，在什么样的深度。这个输出需要有SV_Position语义，并成为一个float4类型。

其他输出（“interpolators”或“varyings”）的顶点着色器是什么您需要产生特定的着色器。从顶点着色器输出的值将在渲染三角形的面内进行插值，每个像素的值将被传递给片段着色器。

许多现代GPU真的不在乎这些变量的语义；但是一些老的系统（最值得注意的是，Shader Model 2的GPU在Direct3D 9）有关于语义的特殊规则：


TEXCOORD0 TEXCOORD1等，用来表示任意高精度数据如纹理坐标和位置。

Color0和COLOR1语义在顶点输出和片段输入是低精度的，0–1范围内的数据（如简单的颜色值）。

最好的跨平台支持，顶点输出和片段输入如TEXCOORDn语义。

####Interpolator count limits
有限制多少插值变量可用于总传递信息从顶点到片段着色器。极限取决于平台和GPU，和一般原则：

```
Up to 8 interpolators: OpenGL ES 2.0 (iOS/Android), Direct3D 11 9.x level (Windows Phone) and Direct3 9 shader model 2.0 (old PCs). Since the interpolator count is limited, but each interpolator can be a 4-component vector, some shaders pack things together to stay within limits. For example, two texture coordinates can be passed in one float4 variable (.xy for one coordinate, .zw for the second coordinate).
Up to 10 interpolators: Direct3D 9 shader model 3.0 (#pragma target 3.0).
Up to 16 interpolators: OpenGL ES 3.0 (iOS/Android), Metal (iOS).
Up to 32 interpolators: Direct3D 10 shader model 4.0 (#pragma target 4.0).
```











