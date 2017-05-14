---
##Pass
Pass块使一个对象的几何形状被渲染一次。



###&emsp;&emsp;语法：

```csharp
    Pass { [Name and Tags] [RenderSetup] }
```
&emsp;&emsp;基本Pass命令包含一个呈现状态设置命令的列表。

---

####● Name and tags

&emsp;&emsp;Pass 可以定义它的名称和任意数量的标签-传递给渲染引擎的传递意图的名称／值的字符串。

---

####● RenderSetup
&emsp;&emsp;设置图形硬件的各种状态，例如α混合应打开，深度测试应使用，等等。这些命令是:


####1. Cull
```csharp
    Cull Back | Front | Off
```
&emsp;&emsp;设置多边形剔除模式。

####2. ZTest
```javascript
    ZTest (Less | Greater | LEqual | GEqual | Equal | NotEqual | Always)
```
设置深度缓冲测试模式。

####3. ZWrite
```javascript
    ZWrite On | Off
```
设置深度缓冲的写模式。

####4. Blend
```javascript
    Blend SourceBlendMode DestBlendMode
    Blend SourceBlendMode DestBlendMode, AlphaSourceBlendMode AlphaDestBlendMode
```
设置α混合模式。

####5. ColorMask
```javascript
    ColorMask RGB | A | 0 | any combination of R, G, B, A
```
设置彩色通道写掩码。设置为0将关闭所有渲染的颜色通道。默认模式是写所有通道（RGBA），但是对于一些特殊的效果，你可能想离开一定的渠道不被修改，或完全禁用颜色写。


####6. Offset
```javascript
    Offset OffsetFactor, OffsetUnits
```
设置Z缓冲深度偏移量。


###● 固定功能着色器命令

     固定功能的照明和材质
```javascript
        Lighting On | Off
        Material { Material Block }
        SeparateSpecular On | Off
        Color Color-value
        ColorMaterial AmbientAndDiffuse | Emission
```
所有这些控制固定功能，每个顶点照明：打开它，设置材料的颜色，打开高光的亮点，提供默认的颜色，如果顶点光照关闭，并控制网格顶点颜色如何影响照明。

####1. Fog
```javascript
    Fog { Fog Block }
```

####2. AlphaTest
```javascript
    AlphaTest ( Less | Greater | LEqual | GEqual | Equal | NotEqual | Always ) CutoffValue
```
####3. Texture Combiners
```javascript
    SetTexture textureProperty { combine options }
```


🔚






