# 并发三大要素：原子性，可见性，有序性

# IsBackground
当IsBackground = true 的时候 启动的这个线程会跟随主线程退出而退出<br>
当IsBackgroun = false的时候，启动的这个线程不会跟随主线程退出而退出

# ManualResetEvent 和 AutoResetEvent
控制线程状态的类  
当初始化为false的时候，会将使用.WaitOne()的线程中断。不往下执行  
当调用Set()的时候，会将状态置为true，此时.WaitOne()就往下执行。当使用AutoResetEvent的时候，会自动的在调用一次WaitOne之后将状态重置为false。  
当调用Reset()的时候，会将状态置为false。

