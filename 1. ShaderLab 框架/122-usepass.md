##UsePass
UsePass 命令使用另一个着色器中命名为Name的pass。

语法
```
    UsePass "Shader/Name"
```

用给定的着色器插入给定名称的所有通道。着色器/名称包含着色器的名称和通道的名称，由斜杠字符分隔。注意，只考虑第一次支持的着色器。

**细节：**

某些着色器从其他着色器中重用现有的，减少代码重复。例如，你可能有一个着色器通过绘制对象的轮廓，你想在其他着色器，通过重用。例如下面的命令使用内置VertexLit材质的通道名称“SHADOWCASTER”:

```
    UsePass "VertexLit/SHADOWCASTER"
```

In order for UsePass to work, a name must be given to the pass one wishes to use. The Name command inside the pass gives it a name:
为了UsePass工作,一个名字必须通过一个愿望。内的名字命令传递给它一个名字:

Name "MyPassName"

Note that internally all pass names are uppercased, so UsePass must refer to the name in uppercase.
注意内部所有通过名称大写,所以UsePass必须参考的名字大写。






