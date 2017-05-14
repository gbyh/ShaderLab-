
##Fallback

&emsp;&emsp;在所有的Subshaders之后可以定义Fallback。“如果没有subshaders可以在这个硬件上运行，尝试使用的另一个着色器”。

####&emsp;&emsp;语法：
```
    Fallback "name"
```

&emsp;&emsp;回退到给定名称name的shader…

```
    Fallback Off
```


&emsp;&emsp;显式地声明，没有Fallback，没有警告应打印，即使没有subshaders能在这个硬件上运行。

---

####&emsp;&emsp;细节：

&emsp;&emsp;回滚语句有同样的效果,仿佛所有subshaders其他材质将插入到它的位置。

---

####示例：
```csharp
    Shader "example" {
        // properties and subshaders here...
        Fallback "otherexample"
    }
```


🔚








