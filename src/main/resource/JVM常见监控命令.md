# JVM常见监控命令


## jps

查看java进程

```
jps -lvm

-q：只输出进程 ID
-m：输出传入 main 方法的参数
-l：输出完全的包名，应用主类名，jar的完全路径名
-v：输出jvm参数
-V：输出通过flag文件传递到JVM中的参数
```


## jstat

查看java进程运行过程中信息

```
jstat [-命令选项] [vmid] [间隔时间/毫秒] [查询次数]

vmid：本地虚拟机进程ID，即当前运行的java进程号
class：类加载的行为统计。
compiler：HotSpt JIT编译器行为统计。
gc：垃圾回收堆的行为统计。
gccapacity：各个垃圾回收代容量(young,old,perm)和他们相应的空间统计。
gcutil：垃圾回收统计概述（百分比）。
gccause：垃圾收集统计概述（同-gcutil），附加最近两次垃圾回收事件的原因。
gcnew：新生代行为统计。
gcnewcapacity：新生代与其相应的内存空间的统计。
gcold：年老代和永生代行为统计。
gcoldcapacity：年老代行为统计。
gcpermcapacity：永生代行为统计。
printcompilation：HotSpot编译方法统计。

```


```
jstat -class pid

类加载的行为统计。

Loaded  Bytes  Unloaded  Bytes     Time   
 15265 38065.0       73   111.3      14.08

Loaded:加载class的数量
Bytes：所占用空间大小
Unloaded：未加载数量
Bytes:未加载占用空间
Time：时间

```

```
jstat -compiler pid

HotSpt JIT编译器行为统计。

Compiled Failed Invalid   Time   FailedType FailedMethod
    7450      2       0   101.13          1 com/meituan/jmonitor/store/httpagent/HttpAgentSender initPostParam


Compiled：编译数量。
Failed：失败数量
Invalid：不可用数量
Time：时间
FailedType：失败类型
FailedMethod：失败的方法
```


```
jstat -gc pid

垃圾回收堆的行为统计。


 S0C    S1C    S0U    S1U      EC       EU        OC         OU       PC     PU    YGC     YGCT    FGC    FGCT     GCT   
524288.0 524288.0  0.0   21637.8 3145728.0 2245185.6 8388608.0  3186264.5  262144.0 122572.4   5559  149.766   1      9.051  158.817

单位:大小-KB；时间-ms

S0C：第一个幸存区的大小
S1C：第二个幸存区的大小
S0U：第一个幸存区的使用大小
S1U：第二个幸存区的使用大小
EC：伊甸园区的大小
EU：伊甸园区的使用大小
OC：老年代大小
OU：老年代使用大小
PC：永久区大小
PU：永久区使用大小
YGC：年轻代垃圾回收次数
YGCT：年轻代垃圾回收消耗时间
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间

```


```
jstat -gccapacity pid

堆内存统计.

NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC      PGCMN    PGCMX     PGC       PC     YGC    FGC 
4194304.0 4194304.0 4194304.0 524288.0 524288.0 3145728.0  8388608.0  8388608.0  8388608.0  8388608.0 262144.0 524288.0 262144.0 262144.0   5608     1


NGCMN：新生代最小容量
NGCMX：新生代最大容量
NGC：当前新生代容量
S0C：第一个幸存区大小
S1C：第二个幸存区的大小
EC：伊甸园区的大小
OGCMN：老年代最小容量
OGCMX：老年代最大容量
OGC：当前老年代大小
OC：当前老年代大小

PGCMN:永久代最小容量
PGCMX：永久代最大容量
PGC：永久代当前容量
PC：永久代当前容量
YGC：年轻代gc次数
FGC：老年代GC次数

```

```

jstat -gcnew pid

新生代垃圾回收统计

S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT  
524288.0 524288.0 18837.8    0.0 15  15 262144.0 3145728.0 150880.4   5652  151.971


S0C：第一个幸存区大小
S1C：第二个幸存区的大小
S0U：第一个幸存区的使用大小
S1U：第二个幸存区的使用大小
TT:对象在新生代存活的次数
MTT:对象在新生代存活的最大次数
DSS:期望的幸存区大小
EC：伊甸园区的大小
EU：伊甸园区的使用大小
YGC：年轻代垃圾回收次数
YGCT：年轻代垃圾回收消耗时间

```

```
jstat -gcnewcapacity pid

新生代内存统计

  NGCMN      NGCMX       NGC      S0CMX     S0C     S1CMX     S1C       ECMX        EC      YGC   FGC 
 4194304.0  4194304.0  4194304.0 524288.0 524288.0 524288.0 524288.0  3145728.0  3145728.0  5667     1

NGCMN：新生代最小容量
NGCMX：新生代最大容量
NGC：当前新生代容量
S0CMX：最大幸存1区大小
S0C：当前幸存1区大小
S1CMX：最大幸存2区大小
S1C：当前幸存2区大小
ECMX：最大伊甸园区大小
EC：当前伊甸园区大小
YGC：年轻代垃圾回收次数
FGC：老年代回收次数

```


```
jstat -gcold pid

老年代垃圾回收统计

 PC       PU        OC          OU       YGC    FGC    FGCT     GCT   
262144.0 122572.9   8388608.0   3194200.5   5680     1    9.051  161.660


PC：方法区大小
PU：方法区使用大小
OC：老年代大小
OU：老年代使用大小
YGC：年轻代垃圾回收次数
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间

```


```

jstat -gcoldcapacity pid
 
 OGCMN       OGCMX        OGC         OC       YGC   FGC    FGCT     GCT   
  8388608.0   8388608.0   8388608.0   8388608.0  5693     1    9.051  161.982


OGCMN：老年代最小容量
OGCMX：老年代最大容量
OGC：当前老年代大小
OC：老年代大小
YGC：年轻代垃圾回收次数
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间
```



```
jstat -gcutil pid

GC使用比例监控

S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT   
3.87   0.00  32.63  38.61  46.76   5970  159.784     1    9.051  168.835


S0：幸存1区当前使用比例
S1：幸存2区当前使用比例
E：伊甸园区使用比例
O：老年代使用比例
P：永久区使用比例
YGC：年轻代垃圾回收次数
YGCT：年轻代垃圾回收消耗时间
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间

```



```
jstat -gcpermcapacity pid

GC永久区数据

 PGCMN      PGCMX       PGC         PC      YGC   FGC    FGCT     GCT   
  262144.0   524288.0   262144.0   262144.0  5755     1    9.051  163.763
  
  PGCMN:最小永久区容量
  PGCMX：最大永久区容量
  PGC：当前永久区容量
  PC：当前永久区容量
  YGC：年轻代垃圾回收次数
  FGC：老年代垃圾回收次数
  FGCT：老年代垃圾回收消耗时间
  GCT：垃圾回收消耗总时间

```

```
jstat -gccause pid

GC原因

  S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT    LGCC                 GCC                 
  0.00   4.31  83.86  38.63  46.76   6017  160.861     1    9.051  169.913 Allocation Failure   No GC   

LGCC：上次GC原因
GCC：本次GC原因
```



```
jstat -printcompilation pid

HotSpot编译方法统计。

Compiled  Size  Type Method
    7457    144    1 java/util/concurrent/SynchronousQueue$TransferStack clean

Compiled：最近编译方法的数量
Size：最近编译方法的字节码数量
Type：最近编译方法的编译类型。
Method：方法名标识。
```


## jinfo

查看正在运行的java应用程序的扩展参数（JVM中-X标示的参数）,支持在运行时修改部分参数。

```
 jinfo vmid 查看正在运行的java应用程序的扩展参数
 jinfo -flag <name> vmid 查看指定扩展参数
 jinfo -flag +- <name> vmid 开启/关闭指定扩展参数
 jinfo -flag <name>=<value> vmid 设置指定扩展参数的值

```

## jmap


主要用于打印指定Java进程(或核心文件、远程调试服务器)的共享对象内存映射或堆内存细节。


```
jmap [ option ] pid 

-dump:[live,]format=b,file=<filename> 使用hprof二进制形式,输出jvm的heap内容到文件, live子选项是可选的，假如指定live选项,那么只输出活的对象到文件。
-finalizerinfo 打印正等候回收的对象的信息。
-heap 打印heap的概要信息，GC使用的算法，heap的配置及wise heap的使用情况。
-histo[:live] 打印每个class的实例数目,内存占用,类全名信息. VM的内部类名字开头会加上前缀”*”. 如果live子参数加上后,只统计活的对象数量。
-permstat 打印classload和jvm heap永久区的信息. 包含每个classloader的名字,活泼性,地址,父classloader和加载的class数量. 另外,内部String的数量和占用内存数也会打印出来。 
-F 强迫.在pid没有响应的时候使用-dump或者-histo参数. 在这个模式下,live子参数无效。 
-h | -help 打印辅助信息。
-J<flag> 传递参数给jmap启动的jvm。
pid 需要被打印配相信息的java进程id,可以用jps查问。

```



```
jmap -heap pid


using parallel threads in the new generation.
using thread-local object allocation.
Concurrent Mark-Sweep GC

Heap Configuration:
   MinHeapFreeRatio = 40
   MaxHeapFreeRatio = 70
   MaxHeapSize      = 7516192768 (7168.0MB)
   NewSize          = 1310720 (1.25MB)
   MaxNewSize       = 17592186044415 MB
   OldSize          = 5439488 (5.1875MB)
   NewRatio         = 3
   SurvivorRatio    = 6
   PermSize         = 268435456 (256.0MB)
   MaxPermSize      = 536870912 (512.0MB)
   G1HeapRegionSize = 0 (0.0MB)

Heap Usage:
New Generation (Eden + 1 Survivor Space):
   capacity = 1644167168 (1568.0MB)
   used     = 1090884360 (1040.3483963012695MB)
   free     = 553282808 (527.6516036987305MB)
   66.34874976411157% used
Eden Space:
   capacity = 1409286144 (1344.0MB)
   used     = 1078069584 (1028.1272735595703MB)
   free     = 331216560 (315.8727264404297MB)
   76.49756499699184% used
From Space:
   capacity = 234881024 (224.0MB)
   used     = 12814776 (12.221122741699219MB)
   free     = 222066248 (211.77887725830078MB)
   5.455858366830008% used
To Space:
   capacity = 234881024 (224.0MB)
   used     = 0 (0.0MB)
   free     = 234881024 (224.0MB)
   0.0% used
concurrent mark-sweep generation:
   capacity = 5637144576 (5376.0MB)
   used     = 81949160 (78.1528091430664MB)
   free     = 5555195416 (5297.847190856934MB)
   1.4537352891195388% used
Perm Generation:
   capacity = 268435456 (256.0MB)
   used     = 138006120 (131.6128921508789MB)
   free     = 130429336 (124.3871078491211MB)
   51.41128599643707% used

38427 interned Strings occupying 4207696 bytes.


```

```
jmap -histo pid 

展示class的内存情况


num     #instances         #bytes  class name
----------------------------------------------
   1:        261745       41836432  <constMethodKlass>
   2:        261745       33514688  <methodKlass>
   3:         15312       27329904  <constantPoolKlass>
   4:          4965       19658744  [Ljava.util.concurrent.ConcurrentHashMap$HashEntry;
   5:        166800       12996624  [C
   6:         15307       11494176  <instanceKlassKlass>
   7:         89565       10795256  [B
   8:         10598       10120960  <constantPoolCacheKlass>
   9:        160477        5135264  java.util.HashMap$Entry
  10:        164645        3951480  java.lang.String
  11:          5321        2776840  <methodDataKlass>
  12:         27353        2188240  java.lang.reflect.Method
  13:         86991        2087784  java.lang.Long
  14:         25183        2001616  [S
  15:         82709        1985016  java.net.InetAddress$InetAddressHolder
  16:         82702        1984848  java.net.Inet4Address
  17:         82237        1973688  java.net.InetSocketAddress$InetSocketAddressHolder
  18:         11172        1853896  [Ljava.util.HashMap$Entry;
  19:         29975        1834488  [[I
  20:         16444        1717208  java.lang.Class

jmap -histo:live 这个命令执行，JVM会先触发gc，然后再统计信息。

可以观察heap中所有对象的情况（heap中所有生存的对象的情况）。包括对象数量和所占空间大小.可以将其
保存到文本中去，在一段时间后，使用文本对比工具，可以对比出GC回收了哪些对象。
```


```
jmap -dump:live,format=b,file=a.log pid

将内存使用的详细情况输出到文件

这个命令执行，JVM会将整个heap的信息dump写入到一个文件，heap如果比较大的话，就会导致这个过程比较耗时，并且执行的过程中为了保证dump的信息是可靠的，所以会暂停应用。
```


## jstack


```
Thread.State

线程状态

1. NEW（新建）

创建后尚未启动的线程处于这个状态。

2. RUNNABLE(可运行)

RUNNABLE状态包括了操作系统线程状态中的Running和Ready，也就是处于此状态的线程可能正在运行，也可能正在等待系统资源，如等待CPU为它分配时间片，如等待网络IO读取数据。

3. BLOCKED（阻塞）

BLOCKED称为阻塞状态.原因通常是它在等待一个“锁”，当尝试进入一个synchronized语句块/方法时，锁已经被其它线程占有，就会被阻塞。或者在调用Object.wait()后，被notify()唤醒，再次进入synchronized语句块/方法时，如果没有获取到锁，会变成BLOCKED。

4. WAITING（无限期等待）

处于这种状态的线程不会被分配CPU执行时间，它们要等待显示的被其它线程唤醒。

以下方法会让线程陷入无限期等待状态：

（1）没有设置timeout参数的Object.wait()

（2）没有设置timeout参数的Thread.join()

（3）LockSupport.park()

5. TIMED_WAITING（限期等待）

处于这种状态的线程也不会被分配CPU执行时间，不过无需等待被其它线程显示的唤醒，在一定时间之后它们会由系统自动的唤醒。

以下方法会让线程进入TIMED_WAITING限期等待状态：

（1）Thread.sleep()方法

（2）设置了timeout参数的Object.wait()方法

（3）设置了timeout参数的Thread.join()方法

（4）LockSupport.parkNanos()方法

（5）LockSupport.parkUntil()方法


6、TERMINATED（结束）

已终止线程的线程状态，线程已经结束执行。换句话说，run()方法走完了，线程就处于这种状态。其实这只是Java语言级别的一种状态，在操作系统内部可能已经注销了相应的线程，或者将它复用给其他需要使用线程的请求，而在Java语言级别只是通过Java代码看到的线程状态而已。


```

```
jstack -l pid

jstack用于生成java虚拟机当前时刻的线程快照。线程快照是当前java虚拟机内每一条线程正在执行的方法堆栈的集合，生成线程快照的主要目的是定位线程出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等。





```
