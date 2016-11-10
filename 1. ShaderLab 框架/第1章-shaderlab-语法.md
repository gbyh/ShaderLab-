##ShaderLab 语法

###语法

```javascript
        Shader "name" { [Properties] Subshaders [Fallback] [CustomEditor] }
```


###示例：

```javascript
        Shader "Simple colored lighting"
        {
            Properties 
            {
                _Color ( "Main Color", Color ) = (1, .5, .5, 1)
            }
        }

        SubShader 
        {
            Pass 
            {
                Material 
                {
                    Diffuse [_Color]
                }
                Lighting On
            }
        }
```