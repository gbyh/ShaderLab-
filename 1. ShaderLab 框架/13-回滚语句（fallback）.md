##Fallback
After all Subshaders a Fallback can be defined. It basically says “if none of subshaders can run on this hardware, try using the ones from another shader”.
毕竟Subshaders应变可以被定义。基本上说,“如果没有subshaders可以在这个硬件上运行,尝试使用的另一个着色器”。

Syntax
Fallback "name"
Fallback to shader with a given name or…
回退到材质与给定名称或…

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












