# synchronized的底层实现

示例：

```java
public class T{
    public static volatile int i=0;
    public static void main(String[] args){
        for(int i=0; i<100000; i++){
            m();
            n();
        }
    }
    public static synchronized void m(){
        
    }
    public static void n(){
        int i=1;
    }
}
```

`JIT(Just In Time) Compiler` 即时编译：将热点代码即时编译成机器语言，几次访问以后就直接编译成机器语言，下次再执行时直接访问机器语言，而不进行编译了。其中m，n函数就是热点代码。

JIT会把m代码，n代码编译成汇编语言，我们通过一个插件**hsdis**（反汇编插件），通过这个插件可以对汇编语言进行翻译，让我们能看到它里面进行了什么样的优化。

> java -XX:+UnlockDiagnosticVMOptions -XX:+PrintAssembly T
>
> 这个得在linux中执行，在执行结果中：
>
> C1 Compile Level 1（一级优化）
>
> C2 Compile Level 2（二级优化）
>
> 找到m() n() 方法的汇编，会看到lock cmpxchg ... 指令



> synchronized实现过程

1、java代码：加一个synchronized 

2、在汇编语言里会在代码里加上 `monitorenter`和`monitorexit`，使用监视器监视锁

3、在整个gvm执行的过程进行锁升级

4、更底层的实现使用的是 ：lock comxchg

> 其他

synchronized在jdk1.0-1.2时，直接使用的重量级锁。效率很低。

自旋锁在用户空间就能解决，不必使用内核态。

jdk1.5之后推出JUC各种锁。



**总结：**

synchronized和volatile的底层实现都是靠lock，大多数的CPU都支持lock指令
