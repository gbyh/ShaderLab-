
## GrabPass

&emsp;&emsp;GrabPass是一个特殊的passtype -它抓住对象所在的屏幕即将进入一个纹理的内容。这种纹理可以在后续的传递中使用先进的基于图像的效果。

####&emsp;&emsp; 语法：

&emsp;&emsp;GrabPass所属subshader。它可以采取两种形式:

&emsp;&emsp;只是GrabPass {}将抓取当前屏幕内容到一个纹理。纹理可以在之后的Pass中通过\_grabtexture名称使用。注：这种形式抓取的pass将对每个对象做昂贵的屏幕抓取操作！

&emsp;&emsp;GrabPass {“TextureName”}将抓取屏幕上的内容转换成纹理，但只会做对每帧中第一个使用给定纹理名字的对象。纹理可以被进一步访问通过给定的纹理名称。当你有多个对象在场景中使用grab pass，这是一个更高效的方式。

&emsp;&emsp;此外,GrabPass可以使用Name和Tags命令。

---

####例如：

&emsp;&emsp;这里是一个昂贵的方式来反转渲染之前的颜色：

```csharp
    Shader "GrabPassInvert"
    {
        SubShader
        {
            // Draw ourselves after all opaque geometry
            Tags { "Queue" = "Transparent" }
    
            // Grab the screen behind the object into _GrabTexture
            GrabPass { }
    
            // Render the object with the texture generated above, and invert the colors
            Pass
            {
                SetTexture [_GrabTexture] { combine one-texture }
            }
        }
    }
```

&emsp;&emsp;这个着色器有两个通道：第一次捕获在渲染时后面的物体，然后在第二遍中应用。当然，同样的效果可以使用反向混合模式便宜得多。


---

```csharp
    Shader "Custom/GrabShader"
    {
        SubShader
        {
            Tags {"Queue"="Transparent" "IgnoreProjector"="True" "RenderType"="Opaque"}
            ZWrite On Lighting Off Cull Off Fog { Mode Off } Blend One Zero
    
            GrabPass { "_GrabTexture" }
    
            Pass
            {
                CGPROGRAM
                #pragma vertex vert
                #pragma fragment frag
                #include "UnityCG.cginc"
    
                sampler2D _GrabTexture;
    
                struct vin_vct
                {
                    float4 vertex : POSITION;
                };
    
                struct v2f_vct
                {
                    float4 vertex : POSITION;
                    float4 uvgrab : TEXCOORD1;
                };
    
                // Vertex function
                v2f_vct vert (vin_vct v)
                {
                    v2f_vct o;
                    o.vertex = mul(UNITY_MATRIX_MVP, v.vertex);
                    o.uvgrab = ComputeGrabScreenPos(o.vertex);
                    return o;
                }
    
                // Fragment function
                half4 frag (v2f_vct i) : COLOR
                {
                    fixed4 col = tex2Dproj( _GrabTexture, UNITY_PROJ_COORD(i.uvgrab));
                    return col;
                }
                ENDCG
            }
        }
    }
```

###&emsp;&emsp; 玻璃着色

```csharp
    Shader "Custom/GlassShader"
    {
        Properties
        {
            _MainTex ("Base (RGB) Trans (A)", 2D) = "white" {}
            _Colour ("Colour", Color) = (1,1,1,1)
    
            _BumpMap ("Noise text", 2D) = "bump" {}
            _Magnitude ("Magnitude", Range(0,1)) = 0.05
        }
    
        SubShader
        {
            Tags {"Queue"="Transparent" "IgnoreProjector"="True" "RenderType"="Opaque"}
            ZWrite On Lighting Off Cull Off Fog { Mode Off } Blend One Zero
    
            GrabPass { "_GrabTexture" }
            Pass
            {
                CGPROGRAM
                #pragma vertex vert
                #pragma fragment frag
                #include "UnityCG.cginc"
    
                sampler2D _GrabTexture;
    
                sampler2D _MainTex;
                fixed4 _Colour;
    
                sampler2D _BumpMap;
                float _Magnitude;
    
                struct vin_vct
                {
                    float4 vertex : POSITION;
                    float4 color : COLOR;
                    float2 texcoord : TEXCOORD0;
                };
    
                struct v2f_vct
                {
                    float4 vertex : POSITION;
                    fixed4 color : COLOR;
                    float2 texcoord : TEXCOORD0;
    
                    float4 uvgrab : TEXCOORD1;
                };
    
                // Vertex function
                v2f_vct vert (vin_vct v)
                {
                    v2f_vct o;
                    o.vertex = mul(UNITY_MATRIX_MVP, v.vertex);
                    o.color = v.color;
    
                    o.texcoord = v.texcoord;
    
                    o.uvgrab = ComputeGrabScreenPos(o.vertex);
                    return o;
                }
    
                // Fragment function
                half4 frag (v2f_vct i) : COLOR
                {
                    half4 mainColour = tex2D(_MainTex, i.texcoord);
                    half4 bump = tex2D(_BumpMap, i.texcoord);
                    half2 distortion = UnpackNormal(bump).rg;
    
                    i.uvgrab.xy += distortion * _Magnitude;
    
                    // tex*proj()内置函数被用来执行投影纹理读取，其中的用来采样的纹理坐标 
                    // 在使用之前会除以第四个分量。
    
                    //UNITY_PROJ_COORD传入四元纹理坐标用于给tex2Dproj读取,但是多数平台上,返回一样的值。
                    fixed4 col = tex2Dproj( _GrabTexture, UNITY_PROJ_COORD(i.uvgrab));
                    return col * mainColour * _Colour;
                }
                ENDCG
            }
        }
    }
```

🔚

