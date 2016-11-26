## 概述
给系统定位问题的时候，知识、经验是关键基础，数据是依据，工具是运用知识处理数据的手段。

java开发人员可以在jdk安装的bin目录下找到除了`java`,`javac`以外的其他命令。这些命令主要是一些用于监视虚拟机和故障处理的工具。这些工具包括：

|名称|主要作用|
|---|---|
|jps|JVM process Status Tool, 显示指定系统内所有的HotSpot虚拟机进程。通常是本地主机|
|jstat|JVM Statistics Monitoring Tool,用于收集HotSpot虚拟机各方面的运行数据|
|jinfo|Configuration Info for java, 显示虚拟机配置信息|
|jmap|Memory Map for Java, 生成虚拟机的内存存储快照（heapdump文件）|
|jhat|JVM Heap Dump Browser, 用于分析heapdump文件，它建立一个HTTP/HTML服务器，让用户可以在浏览器上查看分析结果|
|jstack|Stack Trace for Java, 显示虚拟机的线程快照|

## jps:虚拟机进程状况工具
jps的功能和unix/liunx中的ps命令是类似。只不过它是打印出正在运行的虚拟机进程，并显示虚拟机执行主类的名称以及这些进程的本地虚拟机唯一ID(Local Virtual Machine Identifier, LVMID,通常是系统进程ID)。

*jps命令格式*：
```bash
jps [options] [hostId]
```
jps可以通过RMI协议查询开启了RMI服务的远程虚拟机进程状态，hostId为RMI注册表中注册的主机名称。

*jps其他常用选项*：
```bash
-q 只输出LVMID, 省略主类的名称；
-m 输出虚拟机进程启动时候传递给主类main()函数的参数；
-l 输出主类的全称，如果进程执行的是jar包，输出jar路径；
-v 输出虚拟机进程启动时候JVM参数。
```
*jps命令样例*：
```bash
[root@localhost ~]# jps -l
3914 org.zhangyoubao.payservice.App
12180 sun.tools.jps.Jps
6913 org.zhangyoubao.userprofiler.App
```

## jstat:虚拟机统计信息监视工具
jstat是用于监视虚拟机各种运行状态信息的工具。它可以显示本地或远程虚拟机进程中类load,内存gc.jit等运行参数。

*jstat命令格式*：
```bash
jstat [option vmid [interval [s|ms] [count]]]
```
interval和count代表查询间隔和次数。如果省略这两个参数，说明只查询一次。

*jstat其他常用选项*：
```bash
-class            监视类load/unload数量、总空间已经装载时间；
-compiler         输出JIT编译器编译过的方法、耗时等信息；
-printcompilation 输出已经被JIT编译的方法；
-gc               监视java堆状况；
-gccapacity       监视内容与-gc基本相同，但输出关注java各个区域的最大/最小空间；
-gcutil           监视内容与-gc基本相同，但输出关注已使用空间占用百分百比；
-gccause          与-gcutil功能一样，额外输出导致上一次GC产生原因；
-gcnew            监视新生代GC状况；
-gcnewcapacity    监视新生代，输出同-gccapacity;
-gcold            监视老年代GC状况；
-gcoldcapacity    监视老年代，输出同-gccapacity;
-gcpermcapactiy   监视永久代(代码区),输出同-gccapacity;
```

*jstat命令样例*：
```bash
[root@localhost ~]# jstat -gc 6913
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       PC     PU    YGC     YGCT    FGC    FGCT     GCT   
34048.0 34048.0  0.0   3217.8 272640.0 171092.7  683264.0   168910.7  46872.0 28031.2  37857  380.644  69      3.447  384.091
```

## jinfo:Java配置信息工具
jinfo的作用是实时的查看和调整虚拟机各项参数。

*jinfo命令格式*：
```bash
jinfo [option] pid
```

*jinfo其他常用选项*：
```bash
-flag name=value 修改参数
-flag name 参数参数
```

*jinfo命令样例*：
```bash
[root@localhost ~]# jinfo 6913
Attaching to process ID 6913, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 24.91-b01
Java System Properties:
...

VM Flags:

-Xms1000m -Xmx1000m -Dconf=/usr/local/user_profiler/conf -Dserver.root=/usr/local/user_profiler -Dcom.sun.management.jmxremote.port=7003 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+UseConcMarkSweepGC
```

## jmap:java内存映射工具
jmap命令可以用于生产堆存储快照（dump文件）。它还可以查下finalize队列（自我拯救队列）、java堆和代码区的详细信息。

*jmap命令格式*：
```bash
jmap [option] vmid
```

*jmap其他常用选项*：
```bash
-dump          生成java堆存储快照。格式：-dump:[live,]format=b,file=<filename>;
-finalizerinfo 显示F-Queue中等待Finalizer现象执行finalize方法的对象；
-heap          显示java堆详细信息，如使用哪种回收器、参数配置、分代状况等待；
-histo         显示堆中对象统计信息，包括类、实例书、合计容量；
-permstat      以ClassLoader为统计入口显示永久代内存信息；
-F             当虚拟机进程堆-dump选项没有响应时候，可以使用这个选项强制生成dump快照。
```

*jmap命令样例*：
```bash
[root@localhost ~]# jmap -histo 6913|head -20

 num     #instances         #bytes  class name
----------------------------------------------
   1:       1864966      113459432  [C
   2:        201846       49201192  [B
   3:       1597065       38329560  java.lang.String
   4:        117477       15037056  org.zhangyoubao.thriftdef.UserUsefulInfo
   5:         47104       11072048  [I
   6:        268631        8596192  java.util.HashMap$Entry
   7:         48812        7451760  <constMethodKlass>
   8:        100683        6443712  com.mysql.jdbc.ConnectionPropertiesImpl$BooleanConnectionProperty
   9:         48812        6257856  <methodKlass>
  10:          4230        5271640  <constantPoolKlass>
  11:        159491        5103712  java.util.Hashtable$Entry
  12:        120226        4809040  org.zhangyoubao.common.cache.adv.Node
  13:        127027        4064864  java.util.concurrent.ConcurrentHashMap$HashEntry
  14:        230433        3686928  java.lang.Integer
  15:          3765        3049824  <constantPoolCacheKlass>
  16:         20917        3012048  com.mysql.jdbc.Field
  17:          4230        2943840  <instanceKlassKlass>
```
其中`[C`=`char[]`,`[B`=`byte[]`,`[S`=`short[]`,`[I`=`int[]`,`[[I`=`int[][]`。

#### jhat: 虚拟机堆转存快照分析工具
jhat 命令用于与jmap搭配使用，用来分析jmap生成的dump文件。jhat内置了一个微型的HTTP/HTML服务器，生成的dump文件的分析结果后，可以在浏览器查看。

*jhat命令格式*：
```bash
jmap filename
```

*jhat命令样例*：
```bash
[root@localhost ~]# jhat html_intercept_server.dump 
Reading from html_intercept_server.dump...
Dump file created Wed Nov 23 13:05:33 CST 2016
Snapshot read, resolving...
Resolving 203681 objects...
Chasing references, expect 40 dots........................................
Eliminating duplicate references........................................
Snapshot resolved.
Started HTTP server on port 7000
Server is ready.
```

## jstack:java线程堆栈跟踪工具
jstack用于生成虚拟机当前时刻的线程快照。线程快照就是当前虚拟机每一条线程正在执行的方法堆栈计划，生成线程快照的主要目的是定位线程长时间停顿的原因。在线程停顿的时候，通过jstack来查看没有响应的线程在后台做些什么事情，或者等待着什么资源。

*jstack命令格式*：
```bash
jstack [option] vmid
```

*jstack其他选项*：
```bash
-F 当正常输出的请求不被响应的时候，强制输出线程堆栈；
-l 除了显示堆栈外，显示关于锁的附加信息；
-m 如果调用本地方法，可以显示C/C++的堆栈。
```

*jstack命令样例*：
```bash
[root@localhost ~]# jstack 29577|head -20
2016-11-23 12:58:23
Full thread dump OpenJDK Server VM (24.91-b01 mixed mode):

"pool-1-thread-7261" prio=10 tid=0x0893a400 nid=0x6b0d waiting on condition [0x652ad000]
   java.lang.Thread.State: TIMED_WAITING (parking)
        at sun.misc.Unsafe.park(Native Method)
        - parking to wait for  <0x75b5b400> (a java.util.concurrent.SynchronousQueue$TransferStack)
        at java.util.concurrent.locks.LockSupport.parkNanos(LockSupport.java:226)
        at java.util.concurrent.SynchronousQueue$TransferStack.awaitFulfill(SynchronousQueue.java:460)
        at java.util.concurrent.SynchronousQueue$TransferStack.transfer(SynchronousQueue.java:359)
        at java.util.concurrent.SynchronousQueue.poll(SynchronousQueue.java:942)
        at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1068)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1130)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)

"service_hot_lscs-0" daemon prio=10 tid=0x6982dc00 nid=0x6aeb waiting on condition [0x64ce1000]
   java.lang.Thread.State: TIMED_WAITING (sleeping)
        at java.lang.Thread.sleep(Native Method)
        at org.zhangyoubao.video.client.runner.SimpleVideoRunner.doWork(SimpleVideoRunner.java:150)
```