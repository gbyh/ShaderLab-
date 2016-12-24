##着色器语义

####顶点着色器输入语义

主要的顶点着色器函数（由# pragma指令表示顶点）需要在所有的输入参数的语义。这些对应于单独的网格数据元素，如顶点位置，法线网格，纹理坐标。查看更多细节的顶点程序输入。

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