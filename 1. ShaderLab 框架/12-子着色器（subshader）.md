##子着色器（SubShader）

###语法
```javascript
        SubShader { [Tags] [CommonState] Passdef [Passdef ...] }
```


**示例**
```javascript
        SubShader
        {
            Pass 
            {
                Lightiing Off
                SetTexture [_MainTex] { }
            }
        }
```























