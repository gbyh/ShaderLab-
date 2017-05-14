---
##属性（Properties）

###&emsp&emsp语法

```csharp
        Properties { Property [Property ...] }
```
---
<br>

####定义属性的块。大括号内的多个属性定义如下。

**1、数字和滑动条**
```csharp
        name ("display name", Range (min, max)) = number
        name ("display name", Float) = number
        name ("display name", Int) = number
```
这些都定义了一个数字(标量)属性默认值。Range 形式使它显示滑块之间的最小和最大范围。

**2、颜色和向量**
```csharp
        name ("display name", Color) = (number, number, number, number)
        name ("display name", Vector) = (number, number, number, number)
```
定义了一个颜色属性的默认值给定RGBA组件，或 4d 向量属性默认值。颜色属性有一个颜色选择器显示，并根据色彩空间(见线性照明)需要调整。矢量属性显示为四个数字字段。

**3、纹理贴图**
```csharp        
        name ("display name", 2D) = "defaulttexture" {}        
        name ("display name", Cube) = "defaulttexture" {}        
        name ("display name", 3D) = "defaulttexture" {}
```



**示例**
```csharp
        Properties
        {
            _WaveScale ("Wave scale", Range (0.02, 0.15)) = 0.07           // sliders
            _ReflDistort ("Reflection distort", Range (0, 1.5)) = 0.5
            _RefrDistort ("Refraction distort", Range (0, 1.5)) = 0.4
            _RefrColor ("Refraction color", Color) = (.34, .85, .92, 1)    // color
            _ReflectionTex ("Environment Reflection", 2D) = "" {}          // textures
            _RefractionTex ("Environment Reflection", 2D) = "" {}
            _Fresnel ("Fresnel (A)", 2D) = "" {}
            _BumpMap ("Bumpmap (RGB)", 2D) = "" {}
        }
```


🔚
