---
##CustomEditor

&emsp;&emsp;CustomEditor可以定义你的材质。当你这样做时Unity将寻找一个类，扩展ShaderGUI这个名字。如果找到一个，任何使用这个材质的材料，将使用此ShaderGUI。看到自定义着色器GUI为例子。

####&emsp;&emsp;语法：
```
    CustomEditor "name"
```

&emsp;&emsp;用指定name使用ShaderGUI。

---
####&emsp;&emsp;细节：

&emsp;&emsp;CustomEditor声明影响所有使用这种材质的材质。

---

####示例：
```csharp
    Shader "example" {
        // properties and subshaders here...
        CustomEditor "MyCustomEditor"
    }
```

🔚
