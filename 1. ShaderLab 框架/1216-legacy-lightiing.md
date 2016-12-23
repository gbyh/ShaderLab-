##Legacy Lighting

ShaderLab:传统照明

The material and lighting parameters are used to control the built-in vertex lighting. Vertex lighting is the standard Direct3D/OpenGL lighting model that is computed for each vertex. Lighting on turns it on. Lighting is affected by Material block, ColorMaterial and SeparateSpecular commands.

材料和照明参数用于控制内置顶点照明。顶点光照是标准的Direct3D和OpenGL的光照模型，计算每一个顶点。照明打开它。照明是由料块的影响，材料和separatespecular命令。

Note: Material/Lighting commands have no effect when vertex programs are used; as in that case all calculations are completely described in the shader. It is advisable to use programmable shaders these days instead of legacy vertex lighting. For these you don’t use any of the commands described here, instead you define your own vertex and fragment programs where you do all lighting, texturing and anything else yourself.

注意：当顶点程序使用时，材质/灯光命令没有影响；在这种情况下，所有计算都在着色器中完全描述。建议使用可编程着色器而不是传统的顶点照明。对于这些你不使用任何这里所描述的命令，而是定义自己的顶点和片段程序，完成所有的照明，纹理和任何其他的。

Vertex Coloring & Lighting is the first effect to be calculated for any rendered geometry. It operates on the vertex level, and calculates the base color that is used before textures are applied.

顶点着色和照明是任何渲染几何计算的第一个效果。它在顶点级别上运行，并计算在纹理应用之前使用的基色。

Syntax
The top level commands control whether to use fixed function lighting or not, and some configuration options. The main setup is in the Material Block, detailed further below.
顶级的命令控制是否使用固定功能照明,和一些配置选项。主要设置材料块,下面详细的进一步。

Color
Color color
Sets the object to a solid color. A color is either four RGBA values in parenthesis, or a color property name in square brackets.
将对象设置为一个纯色。颜色是四RGBA括号中的值,在方括号或颜色属性的名字。

Material
Material {Material Block}
The Material block is used to define the material properties of the object.
材料块用于定义材料属性的对象。

Lighting
Lighting On | Off
For the settings defined in the Material block to have any effect, you must enable Lighting with the Lighting On command. If lighting is off instead, the color is taken straight from the Color command.
为材料的块中定义的设置有什么效果,你必须使照明与照明命令。如果照明是相反的,是直接从颜色命令。

SeparateSpecular
SeparateSpecular On | Off
This command makes specular lighting be added to the end of the shader pass, so specular lighting is unaffected by texturing. Only has effect when Lighting On is used.
这个命令让镜面照明被添加到结束的材质,所以反射光是受变形影响。只有当照明使用效果。

ColorMaterial
ColorMaterial AmbientAndDiffuse | Emission
Makes per-vertex color be used instead of the colors set in the material. AmbientAndDiffuse replaces Ambient and Diffuse values of the material; Emission replaces Emission value of the material.
使种每个顶点都具备的颜色而不是使用颜色的材料。AmbientAndDiffuse替换材料的环境和扩散值;发射取代发射材料的价值。

Material Block
This contains settings for how the material reacts to the light. Any of these properties can be left out, in which case they default to black (i.e. have no effect).
它包含设置材料对光线的方式。这些属性可以被排除在外,在这种情况下它们默认为黑色(即没有影响)。

Diffuse color: The diffuse color component. This is an object’s base color.
漫射颜色组件。这是一个对象的基本色。
Ambient color: The ambient color component. This is the color the object has when it’s hit by the ambient light set in the Lighting Window.
环境颜色组件。这是颜色的对象时受环境光照明中设置窗口。
Specular color: The color of the object’s specular highlight.
对象的高光的颜色。
Shininess number: The sharpness of the highlight, between 0 and 1. At 0 you get a huge highlight that looks a lot like diffuse lighting, at 1 you get a tiny speck.
突出显示的清晰度,在0和1之间。0你得到一个巨大的亮点,看起来很像漫射照明,1你会得到一个小斑点。
Emission color: The color of the object when it is not hit by any light.
物体的颜色的时候不受任何光。
The full color of lights hitting the object is:
全彩色的灯光打对象是:
Ambient * Lighting Window’s Ambient Intensity setting + (Light Color * Diffuse + Light Color * Specular) + Emission
The light parts of the equation (within parenthesis) is repeated for all lights that hit the object.
Typically you want to keep the Diffuse and Ambient colors the same (all built-in Unity shaders do this).
光部分的方程(括号内)重复所有灯袭击的对象。通常你想保持扩散和周围颜色相同的(所有内置统一着色器这样做)。

Examples
Always render object in pure red:
总是在纯红色渲染对象:
Shader "Solid Red" {
    SubShader {
        Pass { Color (1,0,0,0) }
    }
}

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






