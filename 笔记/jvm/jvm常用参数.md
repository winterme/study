# jvm 常用参数

### jvm参数
```-XX:+ PrintGCDetails 打印GC详细信息。```
```-XX: +PrintHeapAtGC 每次GC 后，都打印堆的详细信息```。Heap before GC ，Heap after GC
```XX:+PrintGCApplicationStoppedTime``` // 输出GC造成应用暂停的时间
```-XX: +TraceClassLoading```监控类的加载
```-Xloggc:logs/gc.log 指定GC log的路径输出日志文件```
```-XX:+HeapDumpOnOutOfMemoryError OOM时导出堆信息到文件```
```-XX：+HeapDumpPath 导出OOM的路径```
```-XX:OnOutOfMemoryError```在 OOM 的时候执行一个脚本。例如-XX:OnOutOfMemoryError=/xxx/xxx.bat


### 堆的分配参数
```-Xms：指定最小堆大小```。初始堆大小，默认为物理内存的1/64(<1GB)；默认(MinHeapFreeRatio参数可以调整)空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制

```-Xmx：指定最大堆大小```。默认(MaxHeapFreeRatio参数可以调整)空余堆内存大于70%时，JVM会减少堆直到 -Xms的最小限制

```-Xmn：设置新生代的内存空间大小```。注意：此处的大小是（eden+ 2 survivor space)。与jmap -heap中显示的New gen是不同的。整个堆大小=新生代大小 + 老生代大小 + 永久代大小。在保证堆大小不变的情况下，增大新生代后,将会减小老生代大小。此值对系统性能影响较大,Sun官方推荐配置为整个堆的3/8。

```-XX:NewRatio: 老年代（不包含永久区）:新生代（eden + 2*s）```。例如4，即新生代:老年代 = 1:4，即年轻代占堆的1/5

```-XX:SurvivorRatio：新生代中Eden:Survivor容量比值，默认值为8```，即两个Survivor区与一个Eden区的比值为2:8,一个Survivor区占整个年轻代的1/10。


```-Xss：每个线程的堆栈大小```。JDK5.0以后每个线程堆栈大小为1M,以前每个线程堆栈大小为256K。应根据应用的线程所需内存大小进行适当调整。在相同物理内存下,减小这个值能生成更多的线程。但是操作系统对一个进程内的线程数还是有限制的，不能无限生成，经验值在3000~5000左右。一般小的应用， 如果栈不是很深， 应该是128k够用的，大的应用建议使用256k。这个选项对性能影响比较大，需要严格的测试。和threadstacksize选项解释很类似,官方文档似乎没有解释,在论坛中有这样一句话:"-Xss is translated in a VM flag named ThreadStackSize”一般设置这个值就可以了。





```参考文章：https://www.jianshu.com/p/fc0e0df16125```