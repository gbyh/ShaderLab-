
## 传统的 Fog

&emsp;&emsp;用雾命令控制雾参数。

&emsp;&emsp;雾化混合生成的像素到基于摄像头的距离不变的颜色。雾不修改像素的alpha值，只有它的RGB分量。

####&emsp;&emsp;语法：

```
    Fog
    Fog {Fog Commands}
```
<br>



&emsp;&emsp;**指定花括号内的雾命令。**

```
    Mode
    Mode Off | Global | Linear | Exp | Exp2
```

&emsp;&emsp;定义雾模式。默认是全局的，这意味着关闭或EXP2取决于是否打开渲染设置中的雾。

&emsp;&emsp;**设置雾的颜色**
```
    Color
    Color ColorValue

```

####&emsp;&emsp;密度

```
    Density FloatValue
    指数雾集密度。
```

```
    Range
    Range FloatValue, FloatValue
    设置线性雾的 near & far 范围。
```
---

####&emsp;&emsp;细节：

&emsp;&emsp;默认雾设置是基于在照明窗口中设置：雾模式要么是Exp2或关闭；密度和颜色的设置。

>注意：如果您使用片段程序,雾设置材质仍将被应用。平台没有固定函数雾功能，Unity将补丁着色器在运行时支持请求的雾模式。



```csharp

    Fog { Mode Linear Color(0.87, 0.87, 0.87, 1) Density 0.1 Range 0, 300 }
```


🔚
