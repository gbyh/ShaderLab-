##UsePass
UsePass 命令使用另一个着色器中命名为Name的pass。

语法
```
    UsePass "Shader/Name"
```

Inserts all passes with a given name from a given shader. Shader/Name contains the name of the shader and the name of the pass, separated by a slash character. Note that only first supported subshader is taken into account.
用给定的着色器插入给定名称的所有通道。着色器/名称包含着色器的名称和通道的名称，由斜杠字符分隔。注意，只考虑第一次支持的着色器。

Details
Some of the shaders could reuse existing passes from other shaders, reducing code duplication. For example, you might have a shader pass that draws object outline, and you’d want to reuse that pass in other shaders. The UsePass command does just that - it includes a given pass from another shader. As an example the following command uses the pass with the name “SHADOWCASTER” from the built-in VertexLit shader:
的着色器可以重用现有的从其他着色器,减少代码重复。例如,您可能有一个着色器通过吸引对象轮廓,和你想重用,通过其他着色器。UsePass命令就是干这个的,它包括一个从另一个材质。例如下面的命令使用这条通道名称“SHADOWCASTER”与内置VertexLit材质:

UsePass "VertexLit/SHADOWCASTER"

In order for UsePass to work, a name must be given to the pass one wishes to use. The Name command inside the pass gives it a name:
为了UsePass工作,一个名字必须通过一个愿望。内的名字命令传递给它一个名字:

Name "MyPassName"

Note that internally all pass names are uppercased, so UsePass must refer to the name in uppercase.
注意内部所有通过名称大写,所以UsePass必须参考的名字大写。






