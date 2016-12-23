##传统的 Fog

用雾命令控制雾参数。

雾化混合生成的像素到基于摄像头的距离不变的颜色。雾不修改像素的alpha值，只有它的RGB分量。

Syntax
Fog
Fog {Fog Commands}

Specify fog commands inside curly braces.
指定雾在花括号内的命令。

Mode
Mode Off | Global | Linear | Exp | Exp2

Defines fog mode. Default is global, which translates to Off or Exp2 depending whether fog is turned on in Render Settings.
定义了雾模式。默认是全球性的,这意味着根据或者Exp2雾是否打开渲染设置。

Color
Color ColorValue
Sets fog color.

Density
密度

Density FloatValue
Sets density for exponential fog.
集密度指数雾。

Range
Range FloatValue, FloatValue

Sets near & far range for linear fog.
集近与远范围线性雾。

Details
Default fog settings are based on settings in the Lighting Window: fog mode is either Exp2 or Off; density & color taken from settings as well.
默认雾设置是基于在照明窗口中设置:雾模式要么是Exp2或关闭;密度和颜色的设置。

Note that if you use fragment programs, Fog settings of the shader will still be applied. On platforms where there is no fixed function Fog functionality, Unity will patch shaders at runtime to support the requested Fog mode.
注意,如果您使用片段程序,雾设置材质仍将被应用。平台没有固定函数雾功能,统一将补丁着色器在运行时支持请求的雾模式。



