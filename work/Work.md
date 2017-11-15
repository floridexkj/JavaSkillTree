这里是工作中碰到问题

开启b2b项目的时候需要在Debug Configuration标签 添加 VM options参数为：
-server -Xverify:none -Xms1024M -Xmx2048M -XX:+UseParNewGC -XX:PermSize=128m -ea

找到以上参数的解释如下：

-server 使用server jvm。酌情使用，有些doc说IDEA加该选项可以提高速度。 
-Xverify：none 关闭Java字节码验证，从而加快了类装入的速度，并使得在仅为验证目的而启动的过程中无需装入类，缩短了启动时间。

Xms1024m 代表堆内存初始值为1024MB
Xmx2048m 代表堆内存最大值为2048MB

-XX:+UseParNewGC：
使用-XX:+UseParNewGC选项来开启。新生代使用ParNew GC策略，年老代默认使用Serial Old GC策略。
ParNew收集器其实就是Serial收集器的多线程版本。新生代并行，老年代串行；新生代复制算法、老年代标记-压缩

-XX:PermSize=128m
JVM使用-XX:PermSize设置非堆内存初始值，默认是物理内存的1/64；由XX:MaxPermSize设置最大非堆内存的大小，默认是物理内存的1/4。
