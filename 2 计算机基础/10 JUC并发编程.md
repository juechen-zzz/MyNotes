# JUC并发编程

> JUC：java.util.concurrent
>
> 并发（多个线程操作同一个资源）：CPU一核，模拟出多条现场，快速交替
>
> 并行（多个人一起行走）：CPU多核，多个线程可以同时执行

wait和sleep区别：

（1）Thread.sleep  Object.wait

（2）sleep不释放锁，wait释放锁

（3）sleep在任何地方，wait必须在同步代码块中

（4）sleep需要捕获异常，wait不用

## 1 Lock

