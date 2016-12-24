##CustomEditor
A CustomEditor can be defined for your shader. When you do this Unity will look for a class that extends ShaderGUI with this name. If one is found any material that uses this shader will use this ShaderGUI. See Custom Shader GUI for examples.
CustomEditor可以定义你的材质。当你这样做时Unity将寻找一个类,扩展ShaderGUI这个名字。如果找到一个,任何使用这个材质的材料,将使用此ShaderGUI。看到自定义着色器GUI为例子。

语法：
```
    CustomEditor "name"
```

用指定name使用ShaderGUI。

Details
A CustomEditor statement effects all materials that use this Shader
CustomEditor声明影响所有材料,使用这种材质

Example
Shader "example" {
    // properties and subshaders here...
    CustomEditor "MyCustomEditor"
}

