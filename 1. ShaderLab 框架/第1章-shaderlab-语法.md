---
##ShaderLab 语法

###&emsp;&emsp;语法

```csharp
        Shader "name" { [Properties] Subshaders [Fallback] [CustomEditor] }
```

---

###示例：

```csharp
        Shader "Simple colored lighting"
        {
            Properties 
            {
                _Color ( "Main Color", Color ) = (1, .5, .5, 1)
            }

            SubShader 
            {
                Pass 
                {
                    // 使用固定功能着色器的逐顶点光照
                    Material 
                    {
                        Diffuse [_Color]
                    }
                    Lighting On
                }
            }
        }
```

🔚