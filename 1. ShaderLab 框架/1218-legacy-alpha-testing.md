##传统的 Alpha Testing

alpha测试是拒绝将像素写入屏幕的最后机会。

注：当使用片段程序时，透明度测试命令是没有效果的；大多数平台上的测试是使用HLSL着色器做clip()功能。建议使用可编程着色器这些天而不是SetTexture命令。

在计算最终输出颜色后，颜色可以相对于固定值具有alpha值。如果测试失败，则像素不会写入显示器。

####语法：
```
AlphaTest Off
```

渲染所有像素（默认）或…
比较alphavalue AlphaTest

设置alpha测试只渲染alpha值在一定范围内的像素。


####比较
比较是下列单词之一：

|关键字|说明|
|:--|:--|
|Greater|只渲染像素的α大于alphavalue。|
|GEqual|只渲染像素的α大于或等于alphavalue。|
|Less|小于|
|LEqual|小于等于|
|Equal|等于|
|NotEqual|不等于|
|Always|渲染所有像素。这是功能上等同于透明度测试了。|
|Never|不渲染任何像素。|

AlphaValue

介于1和0之间的浮点数。这也可以是一个变量引用类型或范围的属性，在这种情况下，应该使用标准的方括号（[变量名]）。

([VariableName])。

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



