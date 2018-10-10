# Linux常见监控命令


## CPU

1. /proc/cpuinfo文件分析

在Linux系统中，提供了proc文件系统显示系统的软硬件信息。

```
processor	: 0
vendor_id	: GenuineIntel
cpu family	: 6
model		: 6
model name	: QEMU Virtual CPU version 2.5+
stepping	: 3
cpu MHz		: 2199.998
cache size	: 4096 KB
fpu		: yes
fpu_exception	: yes
cpuid level	: 13
wp		: yes
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pse36 clflush mmx fxsr sse sse2 ss syscall nx lm rep_good unfair_spinlock pni pclmulqdq ssse3 cx16 sse4_1 sse4_2 aes xsave avx hypervisor lahf_lm
bogomips	: 4399.99
clflush size	: 64
cache_alignment	: 64
address sizes	: 40 bits physical, 48 bits virtual
power management:
```
```
processor：系统中逻辑处理核的编号
vendor_id：CPU制造商      
cpu family：CPU产品系列代号
model：CPU属于其系列中的哪一代的代号
model name：CPU属于的名字及其编号、标称主频
stepping：CPU属于制作更新版本
cpu MHz：CPU的实际使用主频
cache size：CPU二级缓存大小
physical id：单个CPU的标号
siblings：单个CPU逻辑物理核数
core id：当前物理核在其所处CPU中的编号，这个编号不一定连续
cpu cores：该逻辑核所处CPU的物理核数
apicid：用来区分不同逻辑核的编号
fpu：是否具有浮点运算单元（Floating Point Unit）
fpu_exception：是否支持浮点计算异常
cpuid level：执行cpuid指令前，eax寄存器中的值，根据不同的值cpuid指令会返回不同的内容
wp：表明当前CPU是否在内核态支持对用户空间的写保护（Write Protection）
flags：当前CPU支持的功能
bogomips：在系统内核启动时粗略测算的CPU速度（Million Instructions Per Second）
clflush size：每次刷新缓存的大小单位
cache_alignment：缓存地址对齐单位
address sizes：可访问地址空间位数
```


2. 常用统计命令

```
总核数 = 物理CPU个数 X 每颗物理CPU的核数 
总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

# 查看物理CPU个数
cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

# 查看每个物理CPU中core的个数(即核数)
cat /proc/cpuinfo| grep "cpu cores"| uniq

# 查看逻辑CPU的个数
cat /proc/cpuinfo| grep "processor"| wc -l

```


## 内存

1. free -m 

```
                1          2          3          4         5         6
1             total       used       free     shared    buffers    cached
2 Mem:      24677460   23276064    1401396       0       870540   12084008
3 -/+ buffers/cache:   10321516   14355944
4 Swap:     25151484     224188   24927296

```

```
-b：以Byte为单位显示内存使用情况；
-k：以KB为单位显示内存使用情况；
-m：以MB为单位显示内存使用情况；
-o：不显示缓冲区调节列；
-s<间隔秒数>：持续观察内存使用状况；
-t：显示内存总和列；
-V：显示版本信息。
```

- 第一行的输出时从操作系统（OS）来看的

```
  total：内存总数；
  used：已经使用的内存数；
  free：空闲的内存数；
  shared：当前已经废弃不用；
  buffers：A buffer is something that has yet to be "written" to disk.输出到disk（块设备）的数据；
  cached：A cache is something that has been "read" from the disk and stored for later use. 存放从disk上读出的数据；
```


- 第二行是从一个应用程序的角度看系统内存的使用情况。

```
  -buffers/cache，表示一个应用程序认为系统被用掉多少内存；
  +buffers/cache，表示一个应用程序认为系统还有多少内存；
```
  
- 第三行为交换区的信息，分别是交换的总量（total），使用量（used）和有多少空闲的交换区（free）。
Linux支持虚拟内存（virtual memory），虚拟内存是指使用磁盘当作RAM的扩展，这样可用的内存的大小就相应地增大了。内核会将暂时不用的内存块的内容写到硬盘上，这样一来，这 块内存就可用于其他目的。当需要用到原始的内容时，他们被重新读入内存。这些操作对用户来说是完全透明的；Linux下运行的程式只是看到有大量的内存可 供使用而并没有注意到时不时他们的一部分是驻留在硬盘上的。当然，读写硬盘要比直接使用真实内存慢得多（要慢数千倍），所以程式就不会象一直在内存中运行 的那样快。用作虚拟内存的硬盘部分被称为交换空间（Swap Space）。 







## 硬盘

1. df -h

```
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        25G  4.0G   20G  18% /
tmpfs           937M     0  937M   0% /dev/shm
/dev/vdc1        99G  6.7G   87G   8% /opt
```

2. iostat

```
[patrickxu@vm1 ~]$ iostat
Linux 2.6.32-279.19.3.el6.ucloud.x86_64 (vm1)   06/11/2017  _x86_64_    (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.08    0.00    0.06    0.00    0.00   99.86
           1.
Device:            tps   Blk_read/s   Blk_wrtn/s   Blk_read   Blk_wrtn
vda               0.45         0.29         8.10    6634946  183036680
vdb               0.12         3.11        30.55   70342034  689955328
```
avg-cpu	总体cpu使用情况统计信息，对于多核cpu，这里为所有cpu的平均值。

```
%user
CPU在用户态执行进程的时间百分比。

%nice
CPU在用户态模式下，用于nice操作，所占用CPU总时间的百分比
nice命令以更改过的优先序来执行程序。

%system
CPU处在内核态执行进程的时间百分比

%iowait
CPU用于等待I/O操作占用CPU总时间的百分比

%steal
管理程序(hypervisor)为另一个虚拟进程提供服务而等待虚拟CPU的百分比

%idle
CPU空闲时间百分比
```
- 若 %iowait 的值过高，表示硬盘存在I/O瓶颈 
- 若 %idle 的值高但系统响应慢时，有可能是CPU等待分配内存，此时应加大内存容量 
- 若 %idle 的值持续低于1，则系统的CPU处理能力相对较低，表明系统中最需要解决的资源是 CPU


```
Device：设备名称
tps：每秒向磁盘设备请求数据的次数，包括读、写请求，为rtps与wtps的和。出于效率考虑，每一次IO下发后并不是立即处理请求，而是将请求合并(merge)，这里tps指请求合并后的请求计数。
Blk_read/s：取样时间间隔内每秒读扇区总数量
Blk_wrtn/s：取样时间间隔内每秒写扇区总数量
Blk_read：取样时间间隔内读扇区总数量
Blk_wrtn：取样时间间隔内写扇区总数量
```


```
linux # iostat -x -k -d 1
Linux 2.6.16.60-0.21-smp (linux)     06/13/12

……
Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await  svctm  %util
sda               0.00  9915.00    1.00   90.00     4.00 34360.00   755.25    11.79  120.57   6.33  57.60
```

```
  rrqm/s：每秒对该设备的读请求被合并次数，文件系统会对读取同块(block)的请求进行合并

  wrqm/s：每秒对该设备的写请求被合并次数

  r/s：每秒完成的读次数

  w/s：每秒完成的写次数

  rkB/s：每秒读数据量(kB为单位)

  wkB/s：每秒写数据量(kB为单位)
  
  avgrq-sz：平均每次IO操作的数据量(扇区数为单位)

  avgqu-sz：平均等待处理的IO请求队列长度
  
  await：平均每次IO请求等待时间(包括等待时间和处理时间，毫秒为单位)

  svctm：平均每次IO请求的处理时间(毫秒为单位)

  %util：采用周期内用于IO操作的时间比率，即IO队列非空的时间比率
```



3. du  -h  --max-depth=1

```
--max-depth=<目录层数> 超过指定层数的目录后，予以忽略。
```



## 网络

1. netstat -an用来打印Linux中网络系统的状态信息，可让你得知整个Linux系统的网络情况。
```
-a或--all：显示所有连线中的Socket；
-n或--numeric：直接使用ip地址，而不通过域名服务器；
```
2. traceroute命令用于追踪数据包在网络上的传输时的全部路径，它默认发送的数据包大小是40字节。

3. lsof命令用于查看你进程开打的文件，打开文件的进程，进程打开的端口(TCP、UDP)。

```
-a：列出打开文件存在的进程；
-c<进程名>：列出指定进程所打开的文件；
-g：列出GID号进程详情；
-d<文件号>：列出占用该文件号的进程；
+d<目录>：列出目录下被打开的文件；
+D<目录>：递归列出目录下被打开的文件；
-n<目录>：列出使用NFS的文件；
-i<条件>：列出符合条件的进程。（4、6、协议、:端口、 @ip ）
-p<进程号>：列出指定进程号所打开的文件；
-u：列出UID号进程详情；
-h：显示帮助信息；
-v：显示版本信息。
```


```
command     PID USER   FD      type             DEVICE     SIZE       NODE NAME
init          1 root  cwd       DIR                8,2     4096          2 /
init          1 root  rtd       DIR                8,2     4096          2 /
init          1 root  txt       REG                8,2    43496    6121706 /sbin/init
init          1 root  mem       REG                8,2   143600    7823908 /lib64/ld-2.5.so
init          1 root  mem       REG                8,2  1722304    7823915 /lib64/libc-2.5.so
init          1 root  mem       REG                8,2    23360    7823919 /lib64/libdl-2.5.so
init          1 root  mem       REG                8,2    95464    7824116 /lib64/libselinux.so.1
init          1 root  mem       REG                8,2   247496    7823947 /lib64/libsepol.so.1
init          1 root   10u     FIFO               0,17                1233 /dev/initctl
migration     2 root  cwd       DIR                8,2     4096          2 /
migration     2 root  rtd       DIR                8,2     4096          2 /
```


```
COMMAND：进程的名称
PID：进程标识符
PPID：父进程标识符（需要指定-R参数）
USER：进程所有者
PGID：进程所属组
FD：文件描述符，应用程序通过文件描述符识别该文件。
```



## 综合

1. top命令可以实时动态地查看系统的整体运行情况，是一个综合了多方信息监测系统性能和运行信息的实用工具。通过top命令所提供的互动式界面，用热键可以管理。

```
h：显示帮助画面，给出一些简短的命令总结说明；
k：终止一个进程；
i：忽略闲置和僵死进程，这是一个开关式命令；
q：退出程序；
r：重新安排一个进程的优先级别；
S：切换到累计模式；
s：改变两次刷新之间的延迟时间（单位为s），如果有小数，就换算成ms。输入0值则系统将不断刷新，默认值是5s；
f或者F：从当前显示中添加或者删除项目；
o或者O：改变显示项目的顺序；
l：切换显示平均负载和启动时间信息；
m：切换显示内存信息；
t：切换显示进程和CPU状态信息；
c：切换显示命令名称和完整命令行；
M：根据驻留内存大小进行排序；
P：根据CPU使用百分比大小进行排序；
T：根据时间/累计时间进行排序；
w：将当前设置写入~/.toprc文件中。
```


2. mpstat是Multiprocessor Statistics的缩写，是实时监控工具，报告与cpu的一些统计信息这些信息都存在/proc/stat文件中，在多CPU系统里，其不但能查看所有的CPU的平均状况的信息，而且能够有查看特定的cpu信息，mpstat最大的特点是:可以查看多核心的cpu中每个计算核心的统计数据.

- mpstat -P ALL

```
5:47:55 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest   %idle
05:47:55 PM  all    2.61    0.00    0.50    0.02    0.00    0.04    0.03    0.00   96.79
05:47:55 PM    0    9.96    0.00    1.43    0.07    0.00    0.33    0.19    0.00   88.01
05:47:55 PM    1    1.77    0.00    0.39    0.01    0.00    0.01    0.01    0.00   97.81
05:47:55 PM    2    1.63    0.00    0.39    0.01    0.00    0.00    0.01    0.00   97.95
05:47:55 PM    3    1.60    0.00    0.39    0.01    0.00    0.00    0.01    0.00   97.99
05:47:55 PM    4    1.52    0.00    0.37    0.01    0.00    0.00    0.01    0.00   98.08
05:47:55 PM    5    1.50    0.00    0.35    0.01    0.00    0.00    0.01    0.00   98.12
05:47:55 PM    6    1.48    0.00    0.35    0.01    0.00    0.00    0.01    0.00   98.15
05:47:55 PM    7    1.46    0.00    0.34    0.01    0.00    0.00    0.01    0.00   98.18
```





3. vmstat  3 每个3秒刷新一次

```
procs -----------memory---------- ---swap-- -----io---- -system-- ----cpu----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa
 1  0      0 3498472 315836 3819540    0    0     0     1    2    0  0  0 100  0
```


```
r 运行队列中进程数量，这个值也可以判断是否需要增加CPU。（长期大于1）
b  等待IO的进程数量。

swpd 虚拟内存已使用的大小，如果swpd的值不为0，但是SI，SO的值长期为0，这种情况不会影响系统性能。
free 空闲的物理内存的大小。
buff 用作写缓冲的内存大小。
cache 用作读缓冲的内存大小。

si  每秒从交换区写到内存的大小，由磁盘调入内存。
so   每秒写入交换区的内存大小，由内存调入磁盘。

注意：内存够用的时候，这2个值都是0，如果这2个值长期大于0时，系统性能会受到影响，磁盘IO和CPU资源都会被消耗。有些朋友看到空闲内存（free）很少的或接近于0时，就认为内存不够用了，不能光看这一点，还要结合si和so，如果free很少，但是si和so也很少（大多时候是0），那么不用担心，系统性能这时不会受到影响的。

bi  块设备每秒接收的块数量。
bo 块设备每秒发送的块数量。
注意：随机磁盘读写的时候，这2个值越大（如超出1024k)，能看到CPU在IO等待的值也会越大。


in 每秒CPU的中断次数，包括时间中断。
cs 每秒上下文切换次数。
us 用户CPU时间。
sy 系统CPU时间，如果太高，表示系统调用时间长，例如是IO操作频繁。
id 空闲CPU时间。
wa IO等待时间百分比。
```


4. sar（System Activity Reporter系统活动情况报告）是目前 Linux 上最为全面的系统性能分析工具之一，可以从多方面对系统的活动进行报告。

```
-A：所有报告的总和
-u：输出CPU使用情况的统计信息
-v：输出inode、文件和其他内核表的统计信息
-d：输出每一个块设备的活动信息
-r：输出内存和交换空间的统计信息
-b：显示I/O和传送速率的统计信息
-a：文件读写情况
-c：输出进程统计信息，每秒创建的进程数
-R：输出内存页面的统计信息
-y：终端设备活动情况
-w：输出系统交换活动信息
```




5. pidstat

```
pidstat [ 选项 ] [ <时间间隔> ] [ <次数> ]

-u：默认的参数，显示各个进程的cpu使用统计
-r：显示各个进程的内存使用统计
-d：显示各个进程的IO使用情况
-p：指定进程号
-w：显示每个进程的上下文切换情况
-t：显示选择任务的线程的统计信息外的额外信息
-T { TASK | CHILD | ALL }
这个选项指定了pidstat监控的。TASK表示报告独立的task，CHILD关键字表示报告进程下所有线程统计信息。ALL表示报告独立的task和task下面的所有线程。
注意：task和子线程的全局的统计信息和pidstat选项无关。这些统计信息不会对应到当前的统计间隔，这些统计信息只有在子线程kill或者完成的时候才会被收集。
-V：版本号
-h：在一行上显示了所有活动，这样其他程序可以容易解析。
-I：在SMP环境，表示任务的CPU使用率/内核数量
-l：显示命令名和所有参数
``` 

```
pidstat/pidstat -u -p ALL
查看所有进程的 CPU 使用情况

04:06:12 PM       PID    %usr %system  %guest    %CPU   CPU  Command
04:06:12 PM         1    0.00    0.00    0.00    0.00     0  init
04:06:12 PM         2    0.00    0.00    0.00    0.00     0  kthreadd
04:06:12 PM         3    0.00    0.00    0.00    0.00     0  migration/0
04:06:12 PM         4    0.00    0.00    0.00    0.00     0  ksoftirqd/0
04:06:12 PM         5    0.00    0.00    0.00    0.00     0  migration/0
04:06:12 PM         6    0.00    0.00    0.00    0.00     0  watchdog/0
04:06:12 PM         7    0.00    0.00    0.00    0.00     1  migration/1
04:06:12 PM         8    0.00    0.00    0.00    0.00     1  migration/1
04:06:12 PM         9    0.00    0.00    0.00    0.00     1  ksoftirqd/1
04:06:12 PM        10    0.00    0.00    0.00    0.00     1  watchdog/1
04:06:12 PM        11    0.00    0.00    0.00    0.00     2  migration/2
04:06:12 PM        12    0.00    0.00    0.00    0.00     2  migration/2
0


```
```
PID：进程ID
%usr：进程在用户空间占用cpu的百分比
%system：进程在内核空间占用cpu的百分比
%guest：进程在虚拟机占用cpu的百分比
%CPU：进程占用cpu的百分比
CPU：处理进程的cpu编号
Command：当前进程对应的命令
```


```
pidstat -r
pidstat -r -p pid 指定进程ID的内存使用情况

04:09:53 PM       PID  minflt/s  majflt/s     VSZ    RSS   %MEM  Command
04:09:53 PM         1      0.00      0.00   56028  33392   0.20  init
04:09:53 PM       420      0.00      0.00   10756    836   0.01  udevd
04:09:53 PM       702      0.00      0.00   11312    560   0.00  svscanboot
04:09:53 PM       706      0.00      0.00    4104    220   0.00  svscan
04:09:53 PM       707      0.00      0.00    3920    128   0.00  readproctitle
04:09:53 PM       903      0.00      0.00    9120    660   0.00  dhclient
04:09:53 PM       947      0.00      0.00   93200    708   0.00  auditd
04:09:53 PM       965      0.00      0.00    6160    340   0.00  portreserve
04:09:53 PM      1010      0.00      0.00   71068  46060   0.28  dbus-daemon

pidstat -t -r -p pid

显示选择任务的线程的内存使用情况

04:34:50 PM      TGID       TID  minflt/s  majflt/s     VSZ    RSS   %MEM  Command
04:34:50 PM     12596         -      0.13      0.00 18459652 8192636  50.17  java
04:34:50 PM         -     12596      0.00      0.00 18459652 8192636  50.17  |__java
04:34:50 PM         -     12652      0.00      0.00 18459652 8192636  50.17  |__java
04:34:50 PM         -     12653      0.01      0.00 18459652 8192636  50.17  |__java
04:34:50 PM         -     12654      0.00      0.00 18459652 8192636  50.17  |__java
04:34:50 PM         -     12655      0.01      0.00 18459652 8192636  50.17  |__java
04:34:50 PM         -     12656      0.00      0.00 18459652 8192636  50.17  |__java
04:34:50 PM         -     12657      0.00      0.00 18459652 8192636  50.17  |__java
04:34:50 PM         -     12658      0.00      0.00 18459652 8192636  50.17  |__java
04:34:50 PM         -     12659      0.00      0.00 18459652 8192636  50.17  |__java
04:34:50 PM         -     12660      0.00      0.00 18459652 8192636  50.17  |__java
04:34:50 PM         -     12661      0.00      0.00 18459652 8192636  50.17  |__java
04:34:50 PM         -     12662      0.00      0.00 18459652 8192636  50.17  |__java

```


```
PID：进程标识符
Minflt/s:任务每秒发生的次要错误，不需要从磁盘中加载页
Majflt/s:任务每秒发生的主要错误，需要从磁盘中加载页
VSZ：虚拟地址大小，虚拟内存的使用KB
RSS：常驻集合大小，进程使用的不可交换的物理内存KB
%MEM：进程占用的物理内存百分比
Command：task命令名
```


```

pidstat -d

显示各个进程的IO使用情况

04:17:35 PM       PID   kB_rd/s   kB_wr/s kB_ccwr/s  Command
04:17:37 PM     12596      0.00    384.00      0.00  java
04:17:39 PM     12596      0.00    360.00      0.00  java
04:17:41 PM     12596      0.00    404.00      0.00  java
04:17:43 PM     12596      0.00    330.00      0.00  java
04:17:45 PM     12596      0.00    400.00      0.00  java
04:17:47 PM     12596      0.00    330.00      0.00  java
04:17:49 PM     12596      0.00    314.00      0.00  java


pidstat -t -d -p pid
显示选择任务的线程的IO使用情况

04:33:09 PM      TGID       TID   kB_rd/s   kB_wr/s kB_ccwr/s  Command
04:33:09 PM     12596         -      0.04      1.05      0.00  java
04:33:09 PM         -     12596      0.00      0.00      0.00  |__java
04:33:09 PM         -     12652      0.04      0.00      0.00  |__java
04:33:09 PM         -     12653      0.00      0.00      0.00  |__java
04:33:09 PM         -     12654      0.00      0.00      0.00  |__java
04:33:09 PM         -     12655      0.00      0.00      0.00  |__java
04:33:09 PM         -     12656      0.00      0.00      0.00  |__java
04:33:09 PM         -     12657      0.00      0.00      0.00  |__java
04:33:09 PM         -     12658      0.00      0.00      0.00  |__java
04:33:09 PM         -     12659      0.00      0.00      0.00  |__java
04:33:09 PM         -     12660      0.00      0.00      0.00  |__java
04:33:09 PM         -     12661      0.00      0.00      0.00  |__java
04:33:09 PM         -     12662      0.00      0.00      0.00  |__java
04:33:09 PM         -     12663      0.00      0.00      0.00  |__java
04:33:09 PM         -     12664      0.00      0.00      0.00  |__java
04:33:09 PM         -     12665      0.00      0.00      0.00  |__java
04:33:09 PM         -     12666      0.00      0.00      0.00  |__java
04:33:09 PM         -     12667      0.00      0.00      0.00  |__java
04:33:09 PM         -     12668      0.00      0.00      0.00  |__java
04:33:09 PM         -     12669      0.00      0.00      0.00  |__java
04:33:09 PM         -     12670      0.00      0.00      0.00  |__java
04:33:09 PM         -     12671      0.00      0.00      0.00  |__java
04:33:09 PM         -     12672      0.00      0.00      0.00  |__java
04:33:09 PM         -     12673      0.00      0.00      0.00  |__java
04:33:09 PM         -     12674      0.00      0.00      0.00  |__java
04:33:09 PM         -     12675      0.00      0.00      0.00  |__java
04:33:09 PM         -     12676      0.00      0.00      0.00  |__java
04:33:09 PM         -     12677      0.00      0.00      0.00  |__java
04:33:09 PM         -     12678      0.00      0.00      0.00  |__java

```


```
PID：进程id
kB_rd/s：每秒从磁盘读取的KB
kB_wr/s：每秒写入磁盘KB
kB_ccwr/s：任务取消的写入磁盘的KB。当任务截断脏的pagecache的时候会发生。
COMMAND:task的命令名
```


```
pidstat -w -p pid

显示每个进程的上下文切换情况

04:22:16 PM       PID   cswch/s nvcswch/s  Command
04:22:18 PM     12596      0.00      0.00  java
04:22:20 PM     12596      0.00      0.00  java
04:22:22 PM     12596      0.00      0.00  java
04:22:24 PM     12596      0.00      0.00  java
04:22:26 PM     12596      0.00      0.00  java
04:22:28 PM     12596      0.00      0.00  java

```


```
PID:进程id
Cswch/s:每秒主动任务上下文切换数量
Nvcswch/s:每秒被动任务上下文切换数量
Command:命令名
```


```
pidstat -t -p pid
显示选择任务的线程的统计信息外的额外信息

04:27:01 PM      TGID       TID    %usr %system  %guest    %CPU   CPU  Command
04:27:01 PM     12596         -    0.12    0.02    0.00    0.14     4  java
04:27:01 PM         -     12596    0.00    0.00    0.00    0.00     4  |__java
04:27:01 PM         -     12652    0.00    0.00    0.00    0.00     1  |__java
04:27:01 PM         -     12653    0.00    0.00    0.00    0.00     7  |__java
04:27:01 PM         -     12654    0.00    0.00    0.00    0.00     4  |__java
04:27:01 PM         -     12655    0.00    0.00    0.00    0.00     2  |__java
04:27:01 PM         -     12656    0.00    0.00    0.00    0.00     7  |__java
04:27:01 PM         -     12657    0.00    0.00    0.00    0.00     5  |__java
04:27:01 PM         -     12658    0.00    0.00    0.00    0.00     1  |__java
04:27:01 PM         -     12659    0.00    0.00    0.00    0.00     3  |__java
04:27:01 PM         -     12660    0.00    0.00    0.00    0.00     6  |__java
04:27:01 PM         -     12661    0.00    0.00    0.00    0.00     1  |__java
04:27:01 PM         -     12662    0.00    0.00    0.00    0.00     2  |__java
04:27:01 PM         -     12663    0.00    0.00    0.00    0.00     1  |__java
04:27:01 PM         -     12664    0.00    0.00    0.00    0.00     1  |__java
04:27:01 PM         -     12665    0.00    0.00    0.00    0.00     1  |__java
04:27:01 PM         -     12666    0.00    0.00    0.00    0.00     1  |__java
04:27:01 PM         -     12667    0.00    0.00    0.00    0.00     1  |__java
04:27:01 PM         -     12668    0.00    0.00    0.00    0.00     1  |__java
04:27:01 PM         -     12669    0.00    0.00    0.00    0.00     1  |__java


```


```
TGID:主线程的表示
TID:线程id
%usr：进程在用户空间占用cpu的百分比
%system：进程在内核空间占用cpu的百分比
%guest：进程在虚拟机占用cpu的百分比
%CPU：进程占用cpu的百分比
CPU：处理进程的cpu编号
Command：当前进程对应的命令
```


```
pidstat -T ALL -p pid

04:30:11 PM       PID    %usr %system  %guest    %CPU   CPU  Command
04:30:11 PM     12596    0.12    0.02    0.00    0.14     4  java

04:30:11 PM       PID    usr-ms system-ms  guest-ms  Command
04:30:11 PM     12596  32040220   4873580         0  java

```

```
PID:进程id
Usr-ms:任务和子线程在用户级别使用的毫秒数。
System-ms:任务和子线程在系统级别使用的毫秒数。
Guest-ms:任务和子线程在虚拟机使用的毫秒数。
Command:命令名
```



