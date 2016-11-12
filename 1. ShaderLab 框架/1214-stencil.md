##Stencil （模版）

模板缓冲区具有作为保存或丢弃像素的每个像素掩码的通用作用。


模板缓冲区通常是一个每像素8位的整数。该值可以是书面的，递增或递减。随后绘制调用可以对值进行测试，以决定在运行像素着色之前是否应该被丢弃。


###语法

---

####1. Ref（参考值）
```javascript
    Ref referenceValue
```

相比对的被写入缓存区中的值（除了 `always` 的任何值），如果没有通过，将使用 `Fail` 或者 `ZFail` 的值替代。值为 0～255 的整数。

####2. ReadMask（读遮罩）
```javascript
    ReadMask readMask
```

一个8位掩码为0–255整数，用于比较的参考值与该缓冲区的内容（referenceValue & readMask) 比较方法（stencilBufferValue & readMask）。默认：255

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
在延迟渲染路径中渲染的对象的模板功能是有限的，因为在基本通道和照明通道模板缓冲区用于其他用途。在这两个阶段中，在着色器中定义的模板状态将被忽略，并只在最终 Pass 时考虑。因为它是不可能的，以掩盖这些对象的模板测试的基础上，但他们仍然可以修改缓冲区内容，将被渲染的对象在框架中使用。在延迟路径（例如，透明对象或没有表面着色的对象）的正向渲染路径中呈现的对象将再次设置它们的模板状态。


