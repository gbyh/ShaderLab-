##混合（Blending）

混合用于透明或半透明物体

![](/assets/PipelineBlend.png)

当图形呈现之后，所有着色器已经执行并应用到所有纹理时，像素被绘制到屏幕。他们如何结合由混合命令控制。


###语法：
---

```javascript
    Blend Off: 关闭混合
    Blend SrcFactor DstFactor: 启用混合。生成的颜色乘以 SrcFactor。已经在屏幕上的颜色乘以 DstFactor 和两个加在一起。
```













