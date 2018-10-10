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


