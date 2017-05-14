
---
##Legacy Lighting(传统照明)


&emsp;&emsp;材质和照明参数用于控制内置顶点照明。顶点光照是标准的Direct3D和OpenGL的光照模型，计算每一个顶点。照明打开它。照明是由材质块，材质和separatespecular命令的影响。

>注意：当顶点程序使用时，材质/灯光命令没有影响；在这种情况下，所有计算都在着色器中完全描述。建议使用可编程着色器而不是传统的顶点照明。对于这些你不使用任何这里所描述的命令，而是定义自己的顶点和片段程序，完成所有的照明，纹理和任何其他的。

&emsp;&emsp;顶点着色和照明是任何渲染几何计算的第一个效果。它在顶点级别上运行，并计算在纹理应用之前使用的基色。

###&emsp;&emsp;语法：

&emsp;&emsp;顶层命令控制是否使用固定功能的灯光，以及一些配置选项。主要设置在材料块，进一步详细如下。

---

&emsp;&emsp;**1、Color**

```
    Color color
```
&emsp;&emsp;将对象设置为固体颜色。颜色是四RGBA值在括号，或颜色属性的名称在方括号。

&emsp;&emsp;**2、Material**
```
    Material {
        Material Block
    }
```
&emsp;&emsp;材质块用于定义对象的材质属性。

&emsp;&emsp;**3、Lighting**
```
    Lighting On | Off
```
&emsp;&emsp;对于在材质块中定义的设置有任何影响，您必须启用灯光与灯光的命令。如果灯光是关闭的，颜色是直接从颜色命令。

&emsp;&emsp;**4、SeparateSpecular**
```
    SeparateSpecular On | Off
```
&emsp;&emsp;此命令使镜面照明添加到着色通道的末端，所以镜面照明不受纹理的影响。只有在使用照明时才有效果。

&emsp;&emsp;**5、ColorMaterial**
```
    ColorMaterial AmbientAndDiffuse | Emission
```
&emsp;&emsp;使用每个顶点颜色代替材质中设置的颜色。ambientanddiffuse取代环境和漫反射的材质的值；自发光取代颜色的自发光。

---

**Material Block**

&emsp;&emsp;这包含材质如何反应光的设置。任何这些属性都可以忽略，在这种情况下，它们默认为黑色（即没有影响）。

&emsp;&emsp;**Diffuse color:** 漫反射颜色分量。这是一个对象的基本颜色。

&emsp;&emsp;**Ambient color:** 环境颜色分量。这是对象的颜色，在照明窗口中被环境光照射。

&emsp;&emsp;**Specular color:** 对象的高光的颜色。

&emsp;&emsp;**Shininess number:** 突出的清晰度，在1和0之间。0你得到了一个巨大的亮点，看起来像漫反射照明，1的时候你得到一个小的斑点。

&emsp;&emsp;**Emission color:** 物体不被任何光线击中时的颜色。

>灯光打到物体的全部颜色是:
Ambient * Lighting Window’s Ambient Intensity setting + (Light Color * Diffuse + Light Color * Specular) + Emission
该方程的光部分（括号内）是所有的灯光打在对象重复。
通常你想继续扩散和环境颜色相同的（所有的Unity内置的着色器这样做）。

---

####例如：
&emsp;&emsp;总是纯红色渲染对象:

```csharp

    Shader "Solid Red" {
        SubShader {
            Pass { Color (1,0,0,0) }
        }
    }
```

&emsp;&emsp;基本的着色对象白色和应用顶点照明：

```csharp

    Shader "VertexLit White" {
        SubShader {
            Pass {
                Material {
                    Diffuse (1,1,1,1)
                    Ambient (1,1,1,1)
                }
                Lighting On
            }
        }
    }
```


&emsp;&emsp;扩展版本,增加了在监视面板中的材质颜色属性:

```csharp

    Shader "VertexLit Simple" {
        Properties {
            _Color ("Main Color", COLOR) = (1,1,1,1)
        }
        SubShader {
            Pass {
                Material {
                    Diffuse [_Color]
                    Ambient [_Color]
                }
                Lighting On
            }
        }
    }
```

&emsp;&emsp;最后，一个完整的顶点光照着色器（参见SetTexture参考页）：

```csharp
    Shader "VertexLit" {
        Properties {
            _Color ("Main Color", Color) = (1,1,1,0)
            _SpecColor ("Spec Color", Color) = (1,1,1,1)
            _Emission ("Emmisive Color", Color) = (0,0,0,0)
            _Shininess ("Shininess", Range (0.01, 1)) = 0.7
            _MainTex ("Base (RGB)", 2D) = "white" {}
        }
        SubShader {
            Pass {
                Material {
                    Diffuse [_Color]
                    Ambient [_Color]
                    Shininess [_Shininess]
                    Specular [_SpecColor]
                    Emission [_Emission]
                }
                Lighting On
                SeparateSpecular On
                SetTexture [_MainTex] {
                    Combine texture * primary DOUBLE, texture * primary
                }
            }
        }
    }
```




🔚
