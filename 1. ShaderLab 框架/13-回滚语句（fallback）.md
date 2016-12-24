##Fallback
After all Subshaders a Fallback can be defined. It basically says “if none of subshaders can run on this hardware, try using the ones from another shader”.
在所有的Subshaders之后可以定义Fallback。“如果没有subshaders可以在这个硬件上运行，尝试使用的另一个着色器”。

语法：
```
    Fallback "name"
```

回退到给定名称name的shader…

Fallback Off

Explicitly state that there is no fallback and no warning should be printed, even if no subshaders can run on this hardware.
显式地声明,没有后退,没有警告应打印,即使没有subshaders能在这个硬件上运行。

Details
A fallback statement has the same effect as if all subshaders from the other shader would be inserted into its place.
回滚语句有同样的效果,仿佛所有subshaders其他材质将插入到它的位置。

Example
 	Shader "example" {
        // properties and subshaders here...
        Fallback "otherexample"
    }












