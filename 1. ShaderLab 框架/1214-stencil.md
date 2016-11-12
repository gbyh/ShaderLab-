##Stencil （模版）

模板缓冲区具有作为保存或丢弃像素的每个像素掩码的通用作用。

Stencil 与颜色缓冲区和深度缓冲区类似，模板缓冲区可以为屏幕上的每个像素点保存一个无符号整数值(通常的话是个8位整数)。这个值的具体意义视程序的具体应用而定。在渲染的过程中，可以用这个值与一个预先设定的参考值相比较，根据比较的结果来决定是否更新相应的像素点的颜色值。这个比较的过程被称为模板测试。模板测试发生在透明度测试（alpha test）之后，深度测试（depth test）之前。如果模板测试通过，则相应的像素点更新，否则不更新。



###语法

```javascript
    stencil 
    {
        Ref referenceValue
        ReadMask readMask
        WriteMask writeMask
        Comp comparisonFunction
        Pass stencilOperation
        Fail stencilOperation
        ZFail stencilOperation
    }
```

---

####1. Ref（参考值）
```javascript
    Ref referenceValue
```

与被写入缓存区中的值比较（除了 `always` 的任何值），如果任何一个通过，`Fail` 或 `ZFail` 将取代。referenceValue 是一个取值范围为 0～255 的整数。

####2. ReadMask（读遮罩）
```javascript
    ReadMask readMask
```

一个 8 位掩码为 0～255 整数，用于比较的参考值与该缓冲区的内容（referenceValue & readMask) 比较方法（stencilBufferValue & readMask）。默认：255

####3. WriteMask（写遮罩）
```javascript
    WriteMask writeMask
```
当写入缓存区时使用的一个8位元0～255的遮罩，默认为：255。


####4. Comp（比较）
```javascript
    Comp comparisonFunction
```
该函数用于比较参考值与当前缓存区的值。默认：always。


####5. Pass
```javascript
    Pass stencilOperation
```
如果模板测试（和深度测试）通过，该缓冲区的内容将如何处理。默认：keep。


####6. Fail
```javascript
    Fail stencilOperation
```
如果模板测试失败，将如何处理缓冲区的内容。默认：keep。

####7. ZFail
```javascript
    ZFail stencilOperation
```
如果模板测试通过，但深度测试失败了，将如何处理缓冲区的内容。默认：keep。

>Comp、Pass、Fail 和 ZFail 被应用于几何体的正面，除非指定 Cull Front 时才会被应用于几何体的背面。你也可以明确指定双面模板状态定义 CompFront，PassFront，FailFront，ZFailFront（几何体正面），和 CompBack、PassBack、FailBack、ZFailBack（几何体背面）。


####8. Comparison Function（比较函数）
比较函数是下列之一:

|关键字|说明|
|:--|:--|
|Greater|仅当参考值大于缓冲区中的值的时候渲染像素。|
|GEqual|仅当参考值大于等于缓冲区中的值的时候渲染像素。|
|Less|仅当参考值小于缓冲区中的值的时候渲染像素。|
|LEqual|仅当参考值小于等于缓冲区中的值的时候渲染像素。|
|Equal|仅当参考值等于缓冲区中的值的时候渲染像素。|
|NotEqual|仅当参考值不等于缓冲区中的值的时候渲染像素。|
|Always|使模版测试总是通过|


####9. Stencil Operation（模版操作）

模板操作是下列之一:

|关键字|说明|
|:--|:--|
|Keep|保持缓冲区的当前内容。|
|Zero|将 0 写入缓存区。|
|Replace|将参考值写入缓存区。|
|IncrSat|递增缓存区当前的值，最大到255。|
|DecrSat|递减缓存区当前的值，最小到 0。|
|Invert|使所有的位无效。|
|IncrWrap|递增缓存区当前的值，如果该值已经是 255，那么将变成 0。|
|DecrWrap|递减缓存区当前的值，如果该值已经是 0，那么将变成 255。|


####10. Deferred rendering path（延迟渲染）
在延迟渲染路径中渲染的对象的模板功能是有限的，因为在基本通道和照明通道模板缓冲区用于其他用途。在这两个阶段中，在着色器中定义的模板状态将被忽略，并只在最终 Pass 时考虑。因为遮罩基于模版测试的这些对象是不可能的，但他们仍然可以修改缓冲区内容，被之后渲染在帧中的对象使用。对象在前向渲染路径下的渲染遵循延迟路径（例如，透明对象或没有表面着色的对象） 将模版状态恢复正常。

延迟渲染路径使用模板缓冲区的三个最高位，再加上四个最高位-这取决于在场景中使用了多少光遮罩层。可以在使用模板的读写遮罩的 “干净” 的位范围内工作，或者你可以在照明 Pass 后迫使 Camera 使用 camera.clearstencilafterlightingpass 清洁模版缓冲区。

####示例
第一个示例着色将编写值2，无论深度测试是否通过（模板测试设置为总是通过），如果深度测试失败将减量（和 wrap）当前值为255（假设我们开始用一个干净的模板缓冲区）。

```javascript
    Shader "Red" 
    { 
        SubShader 
        { 
            Tags { "RenderType"="Opaque" "Queue"="Geometry" }  
            Pass 
            { 
                Stencil 
                { 
                    Ref 2           // 参考值为 2，stencilBuffer 值默认为 0 
                    Comp always     // stencil 比较方式是永远通过 
                    Pass replace    // pass 的处理是替换，就是拿 2 替换 buffer 的值 
                    ZFail decrWrap  // ZFail 的处理是溢出型减 1 
                }   
                // 下面是 stencil 和 zbuffer 都通过的话就执行。把点渲染成红色。
  
                CGPROGRAM 
                #pragma vertex vert 
                #pragma fragment frag  

                struct appdata 
                { 
                    float4 vertex : POSITION; 
                };  

                struct v2f 
                { 
                    float4 pos : SV_POSITION; 
                };  

                v2f vert(appdata v) 
                { 
                    v2f o; o.pos = mul(UNITY_MATRIX_MVP, v.vertex); 
                    return o; 
                }  

                half4 frag(v2f i) : SV_Target 
                { 
                    return half4(1, 0, 0, 1); 
                } 
                ENDCG 
            } // end pass
        } // end subshader
    } 
```

![](/assets/stencil_red.png)
>现在在平面以上的点，stencilbuffer值全为2，因为都被replace了。在平面下面的点，通过了stencil测试但是没有通过深度测试，stencil值减一全为255。


第二个着色只会传递给第一个（红色）的像素的像素，因为它是为与值2的值进行检查的。它也将递减缓冲区中的值，无论它失败模板测试


