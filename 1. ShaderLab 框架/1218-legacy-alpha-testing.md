##传统的 Alpha Testing

The alpha test is a last chance to reject a pixel from being written to the screen.
α测试是一个最后的机会拒绝一个像素被写到屏幕上。

Note: AlphaTest commands have no effect when fragment programs are used; on most platforms alpha testing is done in the shader using HLSL clip() function. It is advisable to use programmable shaders these days instead of SetTexture commands.
注意:AlphaTest命令使用片段程序时没有影响;在大多数平台上α测试做的材质使用HLSL夹()函数。建议使用这些天可编程着色器代替SetTexture命令。

After the final output color has been calculated, the color can optionally have its alpha value compared to a fixed value. If the test fails, the pixel is not written to the display.
计算最终的输出颜色后,颜色可以选择α值相对于一个固定的值。如果测试失败,像素不写入显示。

Syntax
AlphaTest Off

Render all pixels (default) or…
AlphaTest comparison AlphaValue

Set up the alpha test to only render pixels whose alpha value is within a certain range.
设置α测试只显示像素的α值是在一定的范围内。

Comparison
比较
Comparison is one of the following words:
比较下列单词:

Greater
Only render pixels whose alpha is greater than AlphaValue.
GEqual
Only render pixels whose alpha is greater than or equal to AlphaValue.
Less
Only render pixels whose alpha value is less than AlphaValue.
LEqual
Only render pixels whose alpha value is less than or equal to from AlphaValue.
Equal
Only render pixels whose alpha value equals AlphaValue.
NotEqual
Only render pixels whose alpha value differs from AlphaValue.
Always
Render all pixels. This is functionally equivalent to AlphaTest Off.
Never
Don’t render any pixels.

AlphaValue
A floating-point number between 0 and 1. This can also be a variable reference to a float or range property, in which case it should be written using the standard square bracket notation ([VariableName]).
浮点数在0和1之间。这也可以是一个浮动或范围属性变量引用,在这种情况下,应该使用标准的方括号([VariableName])。

Details
The alpha test is important when rendering concave objects with transparent parts. The graphics card maintains a record of the depth of every pixel written to the screen. If a new pixel is further away than one already rendered, the new pixel is not written to the display. This means that even with Blending, objects will not show through.
α测试是非常重要的在呈现凹对象与透明的部分。的显卡维护记录每个像素的深度书写到屏幕上。如果一个新的像素远比一个已经呈现,新的像素不写入显示。这意味着即使混合,对象将不会显示。

￼

In this figure, the tree on the left is rendered using AlphaTest. Note how the pixels in it are either completely transparent or opaque. The center tree is rendered using only Alpha Blending - notice how transparent parts of nearby branches cover the distant leaves because of the depth buffer. The tree on the right is rendered using the last example shader - which implements a combination of blending and alpha testing to hide any artifacts.
在这个图中,左边的树使用AlphaTest呈现。注意它的像素是完全透明或不透明的。中心树呈现只使用Alpha混合——注意透明部分附近的树枝覆盖遥远的树叶因为深度缓冲。右边的树呈现使用最后一个例子材质——实现混合和α测试的组合来隐藏任何工件。

Examples
The simplest possible example, assign a texture with an alpha channel to it. The object will only be visible where alpha is greater than 0.5
最简单的例子,分配一个纹理的alpha通道。的对象只能是可见α大于0.5

Shader "Simple Alpha Test" {
    Properties {
        _MainTex ("Base (RGB) Transparency (A)", 2D) = "" {}
    }
    SubShader {
        Pass {
            // Only render pixels with an alpha larger than 50%
            AlphaTest Greater 0.5
            SetTexture [_MainTex] { combine texture }
        }
    }
}

This is not much good by itself. Let us add some lighting and make the cutoff value tweakable:
这不是多好。让我们添加一些照明,使截断值tweakable:

Shader "Cutoff Alpha" {
    Properties {
        _MainTex ("Base (RGB) Transparency (A)", 2D) = "" {}
        _Cutoff ("Alpha cutoff", Range (0,1)) = 0.5
    }
    SubShader {
        Pass {
            // Use the Cutoff parameter defined above to determine
            // what to render.
            AlphaTest Greater [_Cutoff]
            Material {
                Diffuse (1,1,1,1)
                Ambient (1,1,1,1)
            }
            Lighting On
            SetTexture [_MainTex] { combine texture * primary }
        }
    }
}

When rendering plants and trees, many games have the hard edges typical of alpha testing. A way around that is to render the object twice. In the first pass, we use alpha testing to only render pixels that are more than 50% opaque. In the second pass, we alpha-blend the graphic in the parts that were cut away, without recording the depth of the pixel. We might get a bit of confusion as further away branches overwrite the nearby ones, but in practice, that is hard to see as leaves have a lot of visual detail in them.
当呈现植物和树木,许多游戏有硬边α测试的典型。的方式来呈现对象两次。在第一遍,我们使用alpha测试仅呈现像素超过50%不透明。在第二步中,我们alpha-blend图形的部分切掉,没有记录像素的深度。我们可能会有点混乱远分支覆盖附近的,但在实践中,很难看到叶子有很多视觉细节。

Shader "Vegetation" {
    Properties {
        _Color ("Main Color", Color) = (.5, .5, .5, .5)
        _MainTex ("Base (RGB) Alpha (A)", 2D) = "white" {}
        _Cutoff ("Base Alpha cutoff", Range (0,.9)) = .5
    }
    SubShader {
        // Set up basic lighting
        Material {
            Diffuse [_Color]
            Ambient [_Color]
        }
        Lighting On

        // Render both front and back facing polygons.
        Cull Off

        // first pass:
        // render any pixels that are more than [_Cutoff] opaque
        Pass {
            AlphaTest Greater [_Cutoff]
            SetTexture [_MainTex] {
                combine texture * primary, texture
            }
        }

        // Second pass:
        // render in the semitransparent details.
        Pass {
            // Dont write to the depth buffer
            ZWrite off
            // Don't write pixels we have already written.
            ZTest Less
            // Only render pixels less or equal to the value
            AlphaTest LEqual [_Cutoff]

            // Set up alpha blending
            Blend SrcAlpha OneMinusSrcAlpha

            SetTexture [_MainTex] {
                combine texture * primary, texture
            }
        }
    }
}

Note that we have some setup inside the SubShader, rather than in the individual passes. Any state set in the SubShader is inherited as defaults in passes inside it.



