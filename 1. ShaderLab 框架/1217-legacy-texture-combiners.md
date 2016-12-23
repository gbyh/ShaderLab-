##Legacy Texture Combiners（传统的纹理合成）

在计算基本顶点照明后，应用纹理.。在ShaderLab，这是通过使用SetTexture命令。

注意：当片段程序使用时，SetTexture命令没有效果；。建议使用可编程着色器替代SetTexture命令。

固定功能纹理用于做旧风格的组合效果。你可以在一个Pass中有多个SetTexture命令，所有纹理被顺序的应用，如绘图程序的层。SetTexture命令必须放在Pass的最后。

语法：
```
    SetTexture [TextureName] {Texture Block}
```

分配一个纹理。TextureName必须定义为一个纹理属性。如何应用一个在纹理块内定义的纹理。

纹理块控制纹理的应用。在纹理块内可以有两个命令：combine和constantColor。



|纹理块组合命令|说明|
|:--|:--|
|combine src1 * src2|src1 乘 src2，结果是比每个单独的输入要暗。|
|combine src1 + src2|src1 加 src2，结果是比每个单独的输入亮。
|combine src1 - src2|src1 减 src2。|
|combine src1 lerp (src2) src3|使用src2的alpha值在src3和src1之间插值。注意，插值是相反的方向：当alpha是1的时候是src1，当alpha是0的时候使用src3。|
|combine src1 * src2 + src3|src1 乘 scr2的alpha，然后加src3。|
所有的src属性可以是previous、constant、primary或texture其中之一。

Previous 是之前SetTexture的结果。		
Primary 是从照明计算或顶点颜色的颜色。	
Texture 纹理是在SetTexture TextureName指定纹理的颜色（见上图）。
Constant 是ConstantColor指定的颜色。
		
修饰符：

上述公式可由Double或Quad使得颜色亮度2倍或4倍。

所有的src属性的值，除了lerp，可以选择之前一使颜色的否定。	

所有的src属性可以跟一个alpha用来只使用alpha通道。 
		
Texture 块 constantColor 命令：

ConstantColor color: 定义了一个固定颜色，可用于组合命令。

固定功能在Unity 5.0移除。

Unity版本在5之前确实支持纹理坐标转换，在纹理块内使用矩阵命令。如果你现在需要这个功能，考虑重写你的着色器作为可编程着色器代替，并在顶点着色器中进行UV变换。

同样，5删除纹理相结合的模式。如果需要，请在像素着色器中做数学运算。

**细节：**

片段程序存在之前，旧的图形卡使用分层的纹理方法。纹理被一个接一个地应用，修改将被写入屏幕的颜色。对于每个纹理，纹理通常与前一个操作的结果相结合。所以使用实际的片段程序是明智的。


请注意，每个纹理阶段可能会或可能不会被限制到0～1的范围内，这取决于平台。这可能会影响SetTexture阶段，可以产生大于1的值。


**单独的 Alpha & Color 计算**

默认情况下，合并公式用于计算颜色的RGB和alpha分量。可选地，可以指定alpha计算的单独公式.。这看起来像这样：
```
    SetTexture [_MainTex] 
    { 
        combine previous * texture, previous + texture 
    }
```
在这里,我们RGB颜色相乘和叠加alpha。

**高光：**

默认情况下，原色是漫射、环境和镜面颜色的总和（如照明计算中定义的）。如果你将通道设置中的SeparateSpecular打开，镜面的颜色将被添加在合并计算后，而不是之前。这是内置顶点着色器的默认行为。

**图形硬件的支持**

与片段着色器支持现代图形卡（“Shader Model 2的桌面上，OpenGL ES 2移动）支持所有SetTexture模式和至少4个纹理贴图（他们中的许多人支持8）。如果你真的很老的硬件上运行（前2003 PC，或在iPhone3GS手机），你可能有两个纹理阶段低。着色器作者要写他或她想要支持的卡分开着色器。

例如：
Alpha 混合两个纹理

这个小例子需要两个纹理。第一集的第一组合是以_maintex，然后利用_blendtex Alpha通道在_blendtex RGB颜色褪色
这个小例子有两个纹理。第一集第一组合器取_MainTex,然后使用alpha通道_BlendTex淡入_BlendTex的RGB颜色
```
Shader "Examples/2 Alpha Blended Textures" {
    Properties {
        _MainTex ("Base (RGB)", 2D) = "white" {}
        _BlendTex ("Alpha Blended (RGBA) ", 2D) = "white" {}
    }
    SubShader {
        Pass {
            // Apply base texture
            SetTexture [_MainTex] {
                combine texture
            }
            // Blend in the alpha texture using the lerp operator
            SetTexture [_BlendTex] {
                combine texture lerp (texture) previous
            }
        }
    }
}
```

Alpha Controlled Self-illumination
α控制自发光
This shader uses the alpha component of the _MainTex to decide where to apply lighting. It does this by applying the texture to two stages; In the first stage, the alpha value of the texture is used to blend between the vertex color and solid white. In the second stage, the RGB values of the texture are multiplied in.
这个材质使用的α组件_MainTex决定在何处应用照明。它通过纹理应用到两个阶段,在第一阶段,α值之间的纹理混合使用顶点颜色和白色固体。在第二阶段,纹理的RGB值增加。

Shader "Examples/Self-Illumination" {
    Properties {
        _MainTex ("Base (RGB) Self-Illumination (A)", 2D) = "white" {}
    }
    SubShader {
        Pass {
            // Set up basic white vertex lighting
            Material {
                Diffuse (1,1,1,1)
                Ambient (1,1,1,1)
            }
            Lighting On

            // Use texture alpha to blend up to white (= full illumination)
            SetTexture [_MainTex] {
                constantColor (1,1,1,1)
                combine constant lerp(texture) previous
            }
            // Multiply in texture
            SetTexture [_MainTex] {
                combine previous * texture
            }
        }
    }
}


We can do something else for free here, though; instead of blending to solid white, we can add a self-illumination color and blend to that. Note the use of ConstantColor to get a _SolidColor from the properties into the texture blending.
我们可以免费做其他的事情,尽管;而不是混合纯白色,我们可以添加一个自发光的颜色和混合。注意使用ConstantColor得到_SolidColor从性质到纹理混合。

Shader "Examples/Self-Illumination 2" {
    Properties {
        _IlluminCol ("Self-Illumination color (RGB)", Color) = (1,1,1,1)
        _MainTex ("Base (RGB) Self-Illumination (A)", 2D) = "white" {}
    }
    SubShader {
        Pass {
            // Set up basic white vertex lighting
            Material {
                Diffuse (1,1,1,1)
                Ambient (1,1,1,1)
            }
            Lighting On

            // Use texture alpha to blend up to white (= full illumination)
            SetTexture [_MainTex] {
                // Pull the color property into this blender
                constantColor [_IlluminCol]
                // And use the texture's alpha to blend between it and
                // vertex color
                combine constant lerp(texture) previous
            }
            // Multiply in texture
            SetTexture [_MainTex] {
                combine previous * texture
            }
        }
    }
}

And finally, we take all the lighting properties of the vertexlit shader and pull that in:
最后,我们把所有的照明性能vertexlit材质和拉:

Shader "Examples/Self-Illumination 3" {
    Properties {
        _IlluminCol ("Self-Illumination color (RGB)", Color) = (1,1,1,1)
        _Color ("Main Color", Color) = (1,1,1,0)
        _SpecColor ("Spec Color", Color) = (1,1,1,1)
        _Emission ("Emmisive Color", Color) = (0,0,0,0)
        _Shininess ("Shininess", Range (0.01, 1)) = 0.7
        _MainTex ("Base (RGB)", 2D) = "white" {}
    }

    SubShader {
        Pass {
            // Set up basic vertex lighting
            Material {
                Diffuse [_Color]
                Ambient [_Color]
                Shininess [_Shininess]
                Specular [_SpecColor]
                Emission [_Emission]
            }
            Lighting On

            // Use texture alpha to blend up to white (= full illumination)
            SetTexture [_MainTex] {
                constantColor [_IlluminCol]
                combine constant lerp(texture) previous
            }
            // Multiply in texture
            SetTexture [_MainTex] {
                combine previous * texture
            }
        }
    }
}


