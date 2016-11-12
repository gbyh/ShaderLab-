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


