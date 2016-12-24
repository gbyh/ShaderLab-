##表面着色器和DX11镶嵌

表面着色器需要DirectX 11 GPU镶嵌一些支持。思想是：

		镶嵌所显示的是细分：函数名修饰。这个函数计算三角形边缘和内部镶嵌的因素。
		
		当镶嵌使用，“顶点修改”（点：函数名）被调用后镶嵌，每个生成的域着色器顶点。在这里，你通常会位移映射。
		
		表面着色器可以计算Phong镶嵌平滑模型表面甚至没有任何位移映射。


当前的局限性镶嵌支持:

		只有三角域——四、等值线镶嵌。
		当使用镶嵌时,材质是自动编译成着色模型5.0的目标,这意味着它将只在DX11工作。



####没有GPU镶嵌,顶点位移的修饰符

让我们做一些表面着色器位移映射不使用镶嵌的开始。它只是沿着量的基础上从一个顶点法线位移图：

```
Shader "Tessellation Sample" {
        Properties {
            _MainTex ("Base (RGB)", 2D) = "white" {}
            _DispTex ("Disp Texture", 2D) = "gray" {}
            _NormalMap ("Normalmap", 2D) = "bump" {}
            _Displacement ("Displacement", Range(0, 1.0)) = 0.3
            _Color ("Color", color) = (1,1,1,0)
            _SpecColor ("Spec color", color) = (0.5,0.5,0.5,0.5)
        }
        SubShader {
            Tags { "RenderType"="Opaque" }
            LOD 300
            
            CGPROGRAM
            #pragma surface surf BlinnPhong addshadow fullforwardshadows vertex:disp nolightmap
            #pragma target 5.0

            struct appdata {
                float4 vertex : POSITION;
                float4 tangent : TANGENT;
                float3 normal : NORMAL;
                float2 texcoord : TEXCOORD0;
            };

            sampler2D _DispTex;
            float _Displacement;

            void disp (inout appdata v)
            {
                float d = tex2Dlod(_DispTex, float4(v.texcoord.xy,0,0)).r * _Displacement;
                v.vertex.xyz += v.normal * d;
            }

            struct Input {
                float2 uv_MainTex;
            };

            sampler2D _MainTex;
            sampler2D _NormalMap;
            fixed4 _Color;

            void surf (Input IN, inout SurfaceOutput o) {
                half4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
                o.Albedo = c.rgb;
                o.Specular = 0.2;
                o.Gloss = 1.0;
                o.Normal = UnpackNormal(tex2D(_NormalMap, IN.uv_MainTex));
            }
            ENDCG
        }
        FallBack "Diffuse"
    }
```


上面的材质是相当标准的，点的目的：

		顶点修改显示样品的位移图和移动顶点沿其法线。
		它使用自定义顶点数据输入”结构（数据）而不是默认的appdata_full。这是不需要的，但它的镶嵌使用尽可能小的结构是更有效的。
		由于我们的顶点数据没有第二UV坐标，我们添加nolightmap指令排除光照贴图。


Here’s how some simple objects would look like with this shader:
这是一些简单的对象如何看起来像这样材质:

￼


Fixed amount of tessellation
固定数量的镶嵌

Let’s add fixed amount of tessellation, i.e. the same tessellation level for the whole mesh. This approach is suitable if your model’s faces are roughly the same size on screen. Some script could then change the tessellation level from code, based on distance to the camera.
让我们添加固定数量的镶嵌,即相同的镶嵌水平对整个网。这种方法是合适的,如果你的模型的脸大致相同大小的屏幕上。一些脚本可以改变从代码的镶嵌水平,基于与摄像机之间的距离。

Shader "Tessellation Sample" {
        Properties {
            _Tess ("Tessellation", Range(1,32)) = 4
            _MainTex ("Base (RGB)", 2D) = "white" {}
            _DispTex ("Disp Texture", 2D) = "gray" {}
            _NormalMap ("Normalmap", 2D) = "bump" {}
            _Displacement ("Displacement", Range(0, 1.0)) = 0.3
            _Color ("Color", color) = (1,1,1,0)
            _SpecColor ("Spec color", color) = (0.5,0.5,0.5,0.5)
        }
        SubShader {
            Tags { "RenderType"="Opaque" }
            LOD 300
            
            CGPROGRAM
            #pragma surface surf BlinnPhong addshadow fullforwardshadows vertex:disp tessellate:tessFixed nolightmap
            #pragma target 5.0

            struct appdata {
                float4 vertex : POSITION;
                float4 tangent : TANGENT;
                float3 normal : NORMAL;
                float2 texcoord : TEXCOORD0;
            };

            float _Tess;

            float4 tessFixed()
            {
                return _Tess;
            }

            sampler2D _DispTex;
            float _Displacement;

            void disp (inout appdata v)
            {
                float d = tex2Dlod(_DispTex, float4(v.texcoord.xy,0,0)).r * _Displacement;
                v.vertex.xyz += v.normal * d;
            }

            struct Input {
                float2 uv_MainTex;
            };

            sampler2D _MainTex;
            sampler2D _NormalMap;
            fixed4 _Color;

            void surf (Input IN, inout SurfaceOutput o) {
                half4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
                o.Albedo = c.rgb;
                o.Specular = 0.2;
                o.Gloss = 1.0;
                o.Normal = UnpackNormal(tex2D(_NormalMap, IN.uv_MainTex));
            }
            ENDCG
        }
        FallBack "Diffuse"
    }


The tessellation function, tessFixed in our shader, returns four tessellation factors as a single float4 value: tree factors for each edge of the triangle, and one factor for the inside of the triangle. Here, we just return a constant value that is set in material properties.
镶嵌功能,tessFixed着色器,返回四个镶嵌因素作为单个float4值:三角形的每条边的树因素,并为三角形的内部因素之一。在这里,我们只是返回一个恒定值设置材料属性。

￼



Distance-based tessellation
基于距离的镶嵌

We can also change tessellation level based on distance from the camera. For example, we could define two distance values; distance at which tessellation is at maximum (say, 10 meters), and distance towards which tessellation level gradually decreases (say, 20 meters).
我们也可以改变镶嵌水平基于相机的距离。值;例如,我们可以定义两个距离距离,镶嵌在最大(10米),和距离对镶嵌水平逐渐降低(20米)。

Shader "Tessellation Sample" {
        Properties {
            _Tess ("Tessellation", Range(1,32)) = 4
            _MainTex ("Base (RGB)", 2D) = "white" {}
            _DispTex ("Disp Texture", 2D) = "gray" {}
            _NormalMap ("Normalmap", 2D) = "bump" {}
            _Displacement ("Displacement", Range(0, 1.0)) = 0.3
            _Color ("Color", color) = (1,1,1,0)
            _SpecColor ("Spec color", color) = (0.5,0.5,0.5,0.5)
        }
        SubShader {
            Tags { "RenderType"="Opaque" }
            LOD 300
            
            CGPROGRAM
            #pragma surface surf BlinnPhong addshadow fullforwardshadows vertex:disp tessellate:tessDistance nolightmap
            #pragma target 5.0
            #include "Tessellation.cginc"

            struct appdata {
                float4 vertex : POSITION;
                float4 tangent : TANGENT;
                float3 normal : NORMAL;
                float2 texcoord : TEXCOORD0;
            };

            float _Tess;

            float4 tessDistance (appdata v0, appdata v1, appdata v2) {
                float minDist = 10.0;
                float maxDist = 25.0;
                return UnityDistanceBasedTess(v0.vertex, v1.vertex, v2.vertex, minDist, maxDist, _Tess);
            }

            sampler2D _DispTex;
            float _Displacement;

            void disp (inout appdata v)
            {
                float d = tex2Dlod(_DispTex, float4(v.texcoord.xy,0,0)).r * _Displacement;
                v.vertex.xyz += v.normal * d;
            }

            struct Input {
                float2 uv_MainTex;
            };

            sampler2D _MainTex;
            sampler2D _NormalMap;
            fixed4 _Color;

            void surf (Input IN, inout SurfaceOutput o) {
                half4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
                o.Albedo = c.rgb;
                o.Specular = 0.2;
                o.Gloss = 1.0;
                o.Normal = UnpackNormal(tex2D(_NormalMap, IN.uv_MainTex));
            }
            ENDCG
        }
        FallBack "Diffuse"
    }

Here the tessellation function takes three parameters; the vertex data of three triangle corners before tessellation. This is needed to compute tessellation levels, which depend on vertex positions now. We include a built-in helper file Tessellation.cginc and call UnityDistanceBasedTess function from it to do all the work. That function computes distance of each vertex to the camera and derives final tessellation factors.
这里的镶嵌函数接受三个参数;镶嵌前三个三角形的顶点数据的角落。这是需要计算镶嵌水平,取决于顶点位置。我们包括一个内置的辅助文件镶嵌。cginc并调用UnityDistanceBasedTess函数做所有的工作。这个函数计算每个顶点的距离相机,最后镶嵌的因素。


￼



Edge length based tessellation
基于边缘的长度的镶嵌

Purely distance based tessellation is good only when triangle sizes are quite similar. In the image above, you can see that objects that have small triangles are tessellated too much, while objects that have large triangles aren’t tessellated enough.
纯粹的基于距离的镶嵌好只有当三角形大小非常相似。在上面的图片中,你可以看到有小三角形的对象是完全嵌合太多,虽然有大量三角形不够完全嵌合的对象。
Instead, tessellation levels could be computed based on triangle edge length on the screen - the longer the edge, the larger tessellation factor should be applied.
相反,镶嵌水平可以计算基于三角形边缘长度在屏幕上边缘的时间越长,更大的镶嵌因素应该被应用。


Shader "Tessellation Sample" {
        Properties {
            _EdgeLength ("Edge length", Range(2,50)) = 15
            _MainTex ("Base (RGB)", 2D) = "white" {}
            _DispTex ("Disp Texture", 2D) = "gray" {}
            _NormalMap ("Normalmap", 2D) = "bump" {}
            _Displacement ("Displacement", Range(0, 1.0)) = 0.3
            _Color ("Color", color) = (1,1,1,0)
            _SpecColor ("Spec color", color) = (0.5,0.5,0.5,0.5)
        }
        SubShader {
            Tags { "RenderType"="Opaque" }
            LOD 300
            
            CGPROGRAM
            #pragma surface surf BlinnPhong addshadow fullforwardshadows vertex:disp tessellate:tessEdge nolightmap
            #pragma target 5.0
            #include "Tessellation.cginc"

            struct appdata {
                float4 vertex : POSITION;
                float4 tangent : TANGENT;
                float3 normal : NORMAL;
                float2 texcoord : TEXCOORD0;
            };

            float _EdgeLength;

            float4 tessEdge (appdata v0, appdata v1, appdata v2)
            {
                return UnityEdgeLengthBasedTess (v0.vertex, v1.vertex, v2.vertex, _EdgeLength);
            }

            sampler2D _DispTex;
            float _Displacement;

            void disp (inout appdata v)
            {
                float d = tex2Dlod(_DispTex, float4(v.texcoord.xy,0,0)).r * _Displacement;
                v.vertex.xyz += v.normal * d;
            }

            struct Input {
                float2 uv_MainTex;
            };

            sampler2D _MainTex;
            sampler2D _NormalMap;
            fixed4 _Color;

            void surf (Input IN, inout SurfaceOutput o) {
                half4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
                o.Albedo = c.rgb;
                o.Specular = 0.2;
                o.Gloss = 1.0;
                o.Normal = UnpackNormal(tex2D(_NormalMap, IN.uv_MainTex));
            }
            ENDCG
        }
        FallBack "Diffuse"
    }

Here again, we just call UnityEdgeLengthBasedTess function from Tessellation.cginc to do all the actual work.
在这里,我们调用UnityEdgeLengthBasedTess函数从镶嵌。cginc做实际的工作。


￼



For performance reasons, it’s advisable to call UnityEdgeLengthBasedTessCull function instead, which will do patch frustum culling. This makes the shader a bit more expensive, but saves a lot of GPU work for parts of meshes that are outside of camera’s view.
由于性能的原因,建议调用UnityEdgeLengthBasedTessCull函数相反,它将做补丁截头扑杀。这使得材质更贵,但是可以节省大量GPU的工作部件的网格之外的相机的视图。




Phong Tessellation
冯氏镶嵌


Phong Tessellation modifies positions of the subdivided faces so that the resulting surface follows the mesh normals a bit. It’s quite an effective way of making low-poly meshes become more smooth.
冯氏镶嵌细分面临这样的修改位置产生的表面的网格法线。很有效的方式使低多边形网格变得更加平滑。

Unity’s surface shaders can compute Phong tessellation automatically using tessphong:VariableName compilation directive. Here’s an example shader:
Unity的表面着色器可以计算冯氏镶嵌自动使用tessphong:VariableName编译指令。这里有一个例子材质:

Shader "Phong Tessellation" {
        Properties {
            _EdgeLength ("Edge length", Range(2,50)) = 5
            _Phong ("Phong Strengh", Range(0,1)) = 0.5
            _MainTex ("Base (RGB)", 2D) = "white" {}
            _Color ("Color", color) = (1,1,1,0)
        }
        SubShader {
            Tags { "RenderType"="Opaque" }
            LOD 300
            
            CGPROGRAM
            #pragma surface surf Lambert vertex:dispNone tessellate:tessEdge tessphong:_Phong nolightmap
            #include "Tessellation.cginc"

            struct appdata {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
                float2 texcoord : TEXCOORD0;
            };

            void dispNone (inout appdata v) { }

            float _Phong;
            float _EdgeLength;

            float4 tessEdge (appdata v0, appdata v1, appdata v2)
            {
                return UnityEdgeLengthBasedTess (v0.vertex, v1.vertex, v2.vertex, _EdgeLength);
            }

            struct Input {
                float2 uv_MainTex;
            };

            fixed4 _Color;
            sampler2D _MainTex;

            void surf (Input IN, inout SurfaceOutput o) {
                half4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
                o.Albedo = c.rgb;
                o.Alpha = c.a;
            }

            ENDCG
        }
        FallBack "Diffuse"
    }



Here’s a comparison between regular shader (top row) and one that uses Phong tessellation (bottom row). You can see that even without any displacement mapping, the surface becomes more round.
这是一个比较常规的材质(上面一行),另一个使用冯氏镶嵌(底下一行)。您可以看到,即使没有任何位移映射,表面变得更圆。


￼









