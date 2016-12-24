##GrabPass

GrabPass是一个特殊的passtype -它抓住对象所在的屏幕即将进入一个纹理的内容。这种纹理可以在后续的传递中使用先进的基于图像的效果。

####语法：

GrabPass所属subshader。它可以采取两种形式:

只是GrabPass {}将抓取当前屏幕内容到一个纹理。纹理可以在之后的Pass中通过_grabtexture名称使用。注：这种形式抓取的pass将对每个对象做昂贵的屏幕抓取操作！

GrabPass {“TextureName”}将抓取屏幕上的内容转换成纹理，但只会做对每帧中第一个使用给定纹理名字的对象。纹理可以被进一步访问通过给定的纹理名称。当你有多个对象在场景中使用grab pass，这是一个更高效的方式。
		
此外,GrabPass可以使用Name和Tags命令。

例如：

这里是一个昂贵的方式来反转渲染之前的颜色：

```
Shader "GrabPassInvert"
{
    SubShader
    {
        // Draw ourselves after all opaque geometry
        Tags { "Queue" = "Transparent" }

        // Grab the screen behind the object into _GrabTexture
        GrabPass { }

        // Render the object with the texture generated above, and invert the colors
        Pass
        {
            SetTexture [_GrabTexture] { combine one-texture }
        }
    }
}
```

This shader has two passes: First pass grabs whatever is behind the object at the time of rendering, then applies that in the second pass. Now of course, the same effect could be achieved much cheaper using an invert blend mode.
这材质有两个:首先通过抓住一切背后的物体呈现的时候,然后应用在第二步。当然,同样的效果能够达到更便宜的使用一个反混合模式。

See Also
Regular Pass command



