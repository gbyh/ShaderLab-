##Pass Tags

Pass 使用标签告诉渲染引擎如何及何时希望被渲染。


###语法：
---
```javascript
    Tags { "TagName1" = "Value1" "TagName2" = "Value2" }
```
可以有任意多个键值对。

####细节
标签基本上是键-值对。在一个 Pass 标签被用来控制这个通道的作用，在灯光管道（环境，顶点点亮，像素点点亮等）和其他一些选项。注意，下面的标签必须在 Pass 部分而不在 SubShader 部分！
