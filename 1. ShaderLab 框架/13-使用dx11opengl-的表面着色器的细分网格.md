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

```csharp
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

下面是一些简单对象的外观：

![](/assets/SurfaceShaderTess1-none.png)

￼


####固定数量的镶嵌

让我们添加固定数量的镶嵌,即相同的镶嵌水平对整个网。这种方法是合适的,如果你的模型的脸大致相同大小的屏幕上。一些脚本可以改变从代码的镶嵌水平,基于与摄像机之间的距离。
```csharp
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
```

镶嵌功能,tessFixed着色器,返回四个镶嵌因素作为单个float4值:三角形的每条边的树因素,并为三角形的内部因素之一。在这里,我们只是返回一个恒定值设置材料属性。

￼![](/assets/SurfaceShaderTess2-fixed.png)



####基于距离的镶嵌

我们还可以把基于距离相机镶嵌水平。例如，我们可以定义两个距离值；距离，镶嵌在最大（比如说，10米），和距离对镶嵌水平逐渐降低（比如说，20米）。
```csharp
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
```

这里的三角函数需要三个参数；在三角镶嵌三角形顶点数据。这是需要计算的镶嵌水平，这取决于顶点的位置，现在。我们包括一个内置的帮助文件tessellation.cginc叫unitydistancebasedtess功能由它来做所有的工作。这个函数计算每个顶点的距离相机导出最终的细分因子。

![](/assets/SurfaceShaderTess3-distance.png)
￼



####基于边缘的长度的镶嵌

纯粹的基于距离的镶嵌好只有当三角形大小非常相似。在上面的图片中,你可以看到有小三角形的对象是完全嵌合太多,虽然有大量三角形不够完全嵌合的对象。

相反，镶嵌的水平可以基于三角形边长在屏幕的长边计算，应采用较大的细分因子。

```csharp
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
```


在这里，我们称unityedgelengthbasedtess功能Tessellation.cginc做实际工作。

![](/assets/SurfaceShaderTess4-edgelength.png)

￼
出于性能原因，叫unityedgelengthbasedtesscull功能相反它是可取的，它会做补丁的裁剪。这使得着色有点贵，但节省了大量GPU工作部分的网格，在相机的视野。




####Phong Tessellation
冯氏镶嵌


Phong镶嵌修改细分面位置，这样产生的表面网格法线点如下。这是一个非常有效的方法使低多边形网格变得更加光滑。

Unity的表面着色器可以使用tessphong自动计算Phong镶嵌：variablename汇编指令。下面是一个例子着色：

```csharp
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
```


这是间定期材质比较（第一排）和一个使用Phong Tessellation（底部）。你可以看到，即使没有任何位移映射，表面变得更圆。

![](/assets/SurfaceShaderPhongTess.png)
￼









