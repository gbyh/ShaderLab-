##子着色器（SubShader）

###语法
```csharp
    SubShader { [Tags] [CommonState] Passdef [Passdef ...] }
```


**示例**
```csharp
    SubShader
    {
        Pass 
        {
            Lighting Off        // 关闭灯光
            SetTexture [_MainTex] { }
        }
    }
```



🔚



















