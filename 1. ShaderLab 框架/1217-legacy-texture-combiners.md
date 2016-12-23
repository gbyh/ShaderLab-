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
		Previous是SetTexture先前的结果。
		Primary is the color from the lighting calculation or the vertex color if it is bound.
		Primary是颜色的照明计算或顶点颜色如果是绑定。
		Texture is the color of the texture specified by TextureName in the SetTexture (see above).
		指定纹理的颜色纹理的TextureName SetTexture(见上图)。
		Constant is the color specified in ConstantColor.
		常数是ConstantColor中指定的颜色。
Modifiers:
修饰符
		The formulas specified above can optionally be followed by the keywords Double or Quad to make the resulting color 2x or 4x as bright.
		All the src properties, except lerp argument, can optionally be preceded by one - to make the resulting color negated.
		All the src properties can be followed by alpha to take only the alpha channel.
		所有的src属性只能是紧随其后的是αalpha通道。
		
Texture block constantColor command
ConstantColor color: Defines a constant color that can be used in the combine command.
定义了一个不变颜色,可用于组合命令。

Functionality removed in Unity 5.0
功能在Unity 5.0删除

Unity versions before 5.0 did support texture coordinate transformations with a matrix command inside a texture block. If you need this functionality now, consider rewriting your shader as a programmable shader instead, and do the UV transformation in the vertex shader.
Unity版本5.0之前支持纹理坐标转换矩阵纹理块内部的命令。如果你现在需要这个功能,考虑重写你的材质作为可编程着色器相反,顶点着色器和做紫外转换。

Similarly, 5.0 removed signed add (a+-b), multiply signed add (a*b+-c), multiply subtract (a*b-c) and dot product (dot3, dot3rgba) texture combine modes. If you need them, do the math in the pixel shader instead.
同样,5.0删除签名添加(a + b),乘以签署添加(a * b + c),乘减(* c)、点积(dot3 dot3rgba)纹理结合模式。如果你需要它们,在像素着色器而不是做数学。

Details
Before fragment programs existed, older graphics cards used a layered approach to textures. The textures are applied one after each other, modifying the color that will be written to the screen. For each texture, the texture is typically combined with the result of the previous operation. These days it is advisable to use actual fragment programs.
之前片段程序存在,旧显卡使用纹理的分层的方法。应用纹理后,修改的颜色将会写入到屏幕上。对于每一个纹理,纹理通常是结合前面的操作的结果。这些天是明智的使用实际的片段程序。

￼

Note that each texture stage may or might not be clamped to 0..1 range, depending on the platform. This might affect SetTexture stages that can produce values higher than 1.0.
注意,每个纹理阶段可能或可能不会夹到0 . .1,根据平台。这可能影响SetTexture阶段可以产生值高于1.0。

Separate Alpha & Color computation
单独的α&颜色计算

By default, the combiner formula is used for calculating both the RGB and alpha component of the color. Optionally, you can specify a separate formula for the alpha calculation. This looks like this:
默认情况下,合路器公式用于计算颜色的RGB和阿尔法组成部分。可选地,您可以指定一个单独的α的计算公式。这看起来像这样:
SetTexture [_MainTex] { combine previous * texture, previous + texture }

Here, we multiply the RGB colors and add the alpha.
在这里,我们用RGB颜色和添加α。

Specular highlights
高光

By default the primary color is the sum of the diffuse, ambient and specular colors (as defined in the Lighting calculation). If you specify SeparateSpecular On in the pass options, the specular color will be added in after the combiner calculation, rather than before. This is the default behavior of the built-in VertexLit shader.
默认情况下的主要颜色是和分散,环境和镜面的颜色(如照明计算中定义)。在传递选项,如果您指定SeparateSpecular组合器后的高光颜色将被添加在计算,而不是之前。这是默认行为的内置VertexLit材质。

Graphics hardware support
图形硬件的支持

Modern graphics cards with fragment shader support (“shader model 2.0” on desktop, OpenGL ES 2.0 on mobile) support all SetTexture modes and at least 4 texture stages (many of them support 8). If you’re running on really old hardware (made before 2003 on PC, or before iPhone3GS on mobile), you might have as low as two texture stages. The shader author should write separate SubShaders for the cards he or she wants to support.
现代图形卡和片段着色器支持(在桌面“着色模型2.0”,OpenGL ES 2.0移动)支持所有SetTexture模式和至少4纹理阶段(他们中的许多人支持8)。如果你真的老硬件上运行(在2003年之前在电脑,或在iPhone3GS移动),你可能会低至两个纹理阶段。牌的材质作家应该写独立SubShaders他或她想要的支持。

Examples
Alpha Blending Two Textures
This small examples takes two textures. First it sets the first combiner to just take the _MainTex, then is uses the alpha channel of _BlendTex to fade in the RGB colors of _BlendTex
这个小例子有两个纹理。第一集第一组合器取_MainTex,然后使用alpha通道_BlendTex淡入_BlendTex的RGB颜色

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


