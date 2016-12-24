##GrabPass

GrabPass is a special passtype - it grabs the contents of the screen where the object is about to be drawn into a texture. This texture can be used in subsequent passes to do advanced image based effects.
GrabPass是一种特殊的passtype——它抓取屏幕上的内容,对象将被卷入一个纹理。可以使用此结构在后续通过先进的基于图像的影响。

Syntax
The GrabPass belongs inside a subshader. It can take two forms:
GrabPass所属subshader。它可以采取两种形式:

		Just GrabPass { } will grab current screen contents into a texture. The texture can be accessed in further passes by _GrabTexture name. Note: this form of grab pass will do the expensive screen grabbing operation for each object that uses it!
		只是GrabPass { }将抓住当前屏幕内容结构。纹理可以访问进一步经过_GrabTexture名字。注意:这种形式的抓住通过将每个对象的昂贵的屏幕抓取操作,使用它!
		GrabPass { "TextureName" } will grab screen contents into a texture, but will only do that once per frame for the first object that uses the given texture name. The texture can be accessed in further passes by the given texture name. This is a more performant way when you have multiple objects using grab pass in the scene.
		GrabPass {“TextureName”}将抓取屏幕内容到一个纹理,但只会做,一旦第一对象,每帧使用给定的结构名称。纹理可以访问进一步经过给定的结构名称。这是一个更好的性能方法当你有多个对象在现场使用抓住通过。
		
Additionally, GrabPass can use Name and Tags commands.
此外,GrabPass可以使用命令名称和标记。

Example
Here is an expensive way to invert the colors of what was rendered before:
这是一个昂贵的方式转化的颜色呈现的是什么:

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

This shader has two passes: First pass grabs whatever is behind the object at the time of rendering, then applies that in the second pass. Now of course, the same effect could be achieved much cheaper using an invert blend mode.
这材质有两个:首先通过抓住一切背后的物体呈现的时候,然后应用在第二步。当然,同样的效果能够达到更便宜的使用一个反混合模式。

See Also
Regular Pass command



