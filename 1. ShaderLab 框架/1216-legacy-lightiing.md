##Legacy Lighting(传统照明)


材料和照明参数用于控制内置顶点照明。顶点光照是标准的Direct3D和OpenGL的光照模型，计算每一个顶点。照明打开它。照明是由料块的影响，材料和separatespecular命令。

注意：当顶点程序使用时，材质/灯光命令没有影响；在这种情况下，所有计算都在着色器中完全描述。建议使用可编程着色器而不是传统的顶点照明。对于这些你不使用任何这里所描述的命令，而是定义自己的顶点和片段程序，完成所有的照明，纹理和任何其他的。

顶点着色和照明是任何渲染几何计算的第一个效果。它在顶点级别上运行，并计算在纹理应用之前使用的基色。

###语法

顶层命令控制是否使用固定功能的灯光，以及一些配置选项。主要设置在材料块，进一步详细如下。

**Color**

```
    Color color
```
将对象设置为固体颜色。颜色是四RGBA值在括号，或颜色属性的名称在方括号。

**Material**
```
    Material {
        Material Block
    }
```
材质块用于定义对象的材质属性。

**Lighting**
```
    Lighting On | Off
```
对于在材质块中定义的设置有任何影响，您必须启用灯光与灯光的命令。如果灯光是关闭的，颜色是直接从颜色命令。

**SeparateSpecular**
```
    SeparateSpecular On | Off
```
此命令使镜面照明添加到着色通道的末端，所以镜面照明不受纹理的影响。只有在使用照明时才有效果。

**ColorMaterial**
```
    ColorMaterial AmbientAndDiffuse | Emission
```
使用每个顶点颜色代替材质中设置的颜色。ambientanddiffuse取代环境和漫反射的材质的值；自发光取代颜色的自发光。

**Material Block**

这包含材料如何反应光的设置。任何这些属性都可以忽略，在这种情况下，它们默认为黑色（即没有影响）。

Diffuse color: 漫反射颜色分量。这是一个对象的基本颜色。

Ambient color: 环境颜色分量。这是对象的颜色，在照明窗口中被环境光照射。

Specular color: 对象的高光的颜色。

Shininess number: 突出的清晰度，在1和0之间。0你得到了一个巨大的亮点，看起来像漫反射照明，1的时候你得到一个小的斑点。

Emission color: 物体不被任何光线击中时的颜色。

灯光打到物体的全部颜色是:
Ambient * Lighting Window’s Ambient Intensity setting + (Light Color * Diffuse + Light Color * Specular) + Emission
该方程的光部分（括号内）是所有的灯光打在对象重复。
通常你想继续扩散和环境颜色相同的（所有的Unity内置的着色器这样做）。


例如：
总是纯红色渲染对象:
```
Shader "Solid Red" {
    SubShader {
        Pass { Color (1,0,0,0) }
    }
}
```
Basic Shader that colors the object white and applies vertex lighting:
基本的材质,颜色白色和适用对象顶点照明:
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

An extended version that adds material color as a property visible in Material Inspector:
延长版本,增加了材料颜色属性可见材料检查员:
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

And finally, a full fledged vertex-lit shader (see also SetTexture reference page):
最后,一个完整的vertex-lit材质(见还SetTexture参考页面):

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






