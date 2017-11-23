 **工作中碰到问题**

开启b2b项目的时候需要在Debug Configuration标签 添加 VM options参数为：
>-server -Xverify:none -Xms1024M -Xmx2048M -XX:+UseParNewGC -XX:PermSize=128m -ea

找到以上参数的解释如下：

-server 使用server jvm。酌情使用，有些doc说IDEA加该选项可以提高速度。 
-Xverify：none 关闭Java字节码验证，从而加快了类装入的速度，并使得在仅为验证目的而启动的过程中无需装入类，缩短了启动时间。

Xms1024m 代表堆内存初始值为1024MB
Xmx2048m 代表堆内存最大值为2048MB

-XX:+UseParNewGC：
使用-XX:+UseParNewGC选项来开启。新生代使用ParNew GC策略，年老代默认使用Serial Old GC策略。
ParNew收集器其实就是Serial收集器的多线程版本。**新生代并行，老年代串行；新生代复制算法、老年代标记-压缩**

-XX:PermSize=128m
JVM使用-XX:PermSize设置非堆内存初始值，默认是物理内存的1/64；由XX:MaxPermSize设置最大非堆内存的大小，默认是物理内存的1/4。

《深入理解jvm虚拟机》书上看到
>使用ParNew、Serial等带有Compact过程的收集器时，系统使用的分配算法是指针碰撞（假设Java堆中内存绝对规整，所有用过的内从放到一边，空闲的放到另一边，中间放着一个指针作为分界点的指示器，那所分配内存就是仅仅把指针向空闲的空间那边挪动一段待分配对象大小相等的距离），相对立的使用CMS这种基于Mark-Sweep算法的收集器时，系统采用空闲列表分配方式（堆中内存并不规整，已使用和空闲内存相互交错，虚拟机就必须维护一个列表，记录上哪些内存块是可用的，再分配时从列表上找到一块足够大的空间划分给对象实例，并更新列表上的实例）。


上面是我本地b2b的服务jvm配置

下面是测试环境的配置：
>JAVA_OPTS=-server -Xms5120M -Xmx5120M -Xmn512M -Xss256k 
-XX:+AggressiveOpts -XX:+UseBiasedLocking -XX:PermSize=1024M 
-XX:MaxPermSize=1024M -XX:+DisableExplicitGC -XX:MaxTenuringThreshold=31 
-XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:+CMSParallelRemarkEnabled 
-XX:+UseCMSCompactAtFullCollection -XX:LargePageSizeInBytes=128M 
-XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly

-Xss256k 栈容量设置

-XX:+AggressiveOpts	加快编译

-XX:+UseBiasedLocking	锁机制的性能改善

-XX:+DisableExplicitGC	关闭System.gc()	 	这个参数需要严格的测试

-XX:MaxTenuringThreshold	垃圾最大年龄	 	如果设置为0的话,则年轻代对象不经过Survivor区,直接进入年老代. 对于年老代比较多的应用,可以提高效率.如果将此值设置为一个较大值,则年轻代对象会在Survivor区进行多次复制,这样可以增加对象再年轻代的存活时间,增加在年轻代即被回收的概率
该参数只有在串行GC时才有效.

-XX:+UseConcMarkSweepGC	使用CMS内存收集	 	测试中配置这个以后,-XX:NewRatio=4的配置失效了,原因不明.所以,此时年轻代大小最好用-Xmn设置.???  对年老代

-XX:+CMSParallelRemarkEnabled	降低标记停顿

-XX:+UseCMSCompactAtFullCollection:使用并发收集器时,开启对年老代的压缩.

-XX:LargePageSizeInBytes	内存页的大小不可设置过大， 会影响Perm的大小	 	=128m

-XX:+UseFastAccessorMethods	原始类型的快速优化

-XX:+UseCMSInitiatingOccupancyOnly	使用手动定义初始化定义开始CMS收集	 	禁止hostspot自行触发CMS GC


昨天晚上上线碰到一个数据权限的问题，碰到一个mysql标签不懂，有时间整理一下

昨天碰到idea如果在方法名上打断点，启动就会特别慢。



    <tx:advice id="txAdvice" transaction-manager="transactionManager">
       <tx:attributes>
             <tx:method name="get*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="find*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="search*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="list*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="is*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="has*" propagation="SUPPORTS" read-only="true" />
             <tx:method name="*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
		</tx:attributes>
    </tx:advice>

    <aop:config proxy-target-class="true">
        <aop:pointcut id="interceptorPointCuts"
            expression="execution(* com.b2bex.*.service.*Manager.*(..)) or execution(* com.autozi.*.*.service.*Manager.*(..))" />
        <aop:advisor advice-ref="txAdvice"
            pointcut-ref="interceptorPointCuts" />
    </aop:config>
    
项目里配置通过interceptorPointCuts对manager层的方法都加上事务回滚操作。

昨天把for循环然后update操作 写在了mangager层导致 事务嵌套事务 导致所有的更新操作都被失效，被回滚，没法提交了。