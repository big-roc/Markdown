原文链接：https://blog.csdn.net/kaede1209/article/details/81953262

### Shuffle过程详解

### MapReduce

MapReduce整个过程可以分为split、map、shuffle和reduce四个过程。

Map是映射，负责数据的过滤分发。Reduce是规约，负责数据的计算归并。Reduce的数据来源于Map，Map的输出即是Reduce的输入，Reduce需要通过Shuffle来获取数据。

从Map输出到Reduce输入的整个过程可以广义的称为Shuffle。Shuffle横跨Map端和Reduce端，在Map端包括Spill过程，在Reduce端包括copy和sort过程。如图所示：

![shuffle](E:\大鹏\总结\Hadoop\shuffle.jpg)

### Map的shuffle过程

Spill过程包括输出、排序、溢写、合并等步骤。如图所示：

![MapShuffle](E:\大鹏\总结\Hadoop\MapShuffle.png)

- Collect：每个Map任务不断地以键值对的形式把数据输出到内存中构造的一个环形数据结构中。使用环形数据结构是为了更有效地使用内存空间，在内存中放置尽可能多的数据。这个数据结构其实就是个字节数组，叫KvBuffer

- Sort：先把KvBuffer中的数据按照partition值和key两个关键字升序排序，移动的只是索引数据，排序结果是KvMeta中数据按照partition为单位聚集在一起，同一个partition内的按照key有序。

- Spill：Spill线程为这次Spill过程创建一个磁盘文件：从所有的本地目录中轮询查找能存储这么大空间的目录，找到之后在其中创建一个类似于 "spill12.out"的文件。Spill线程根据排过序的KvMeta挨个partition的把数据吐到这个文件中，一个partition对应的数据吐完之后顺序地吐下个partition，直到把所有的partition遍历完。一个partition在文件中对应的数据也叫段(segment)。

  内存缓冲区是有大小限制的，默认是100MB。当map task的输出结果很多时，就可能会撑爆内存，所以需要在一定条件下将缓冲区中的数据临时写入磁盘，然后重新利用这块缓冲区。这个从内存往磁盘写数据的过程被称为Spill，中文可译为溢写。比例默认是0.8，也就是当缓冲区的数据达到阈值（buffer size * spill percent = 100MB * 0.8 = 80MB），溢写线程启动，锁定这80MB的内存，执行溢写过程。Map task的输出结果还可以往剩下的20MB内存中写，互不影响。

- Merge：Map任务如果输出数据量很大，可能会进行好几次Spill，out文件和Index文件会产生很多，分布在不同的磁盘上，最后把这些文件进行合并的merge过程闪亮登场。

### Reduce的shuffle过程

- Copy：Reduce任务通过HTTP向各个Map任务拖取它所需要的数据。每个节点都会启动一个常驻的HTTP server，其中一项服务就是响应Reduce拖取Map数据。当有MapOutput的HTTP请求过来的时候，HTTP server就读取相应的Map输出文件中对应这个Reduce部分的数据通过网络流输出给Reduce。

- Merge Sort：这里使用的Merge和Map端使用的Merge过程一样。Map的输出数据已经是有序的，Merge进行一次合并排序，所谓Reduce端的sort过程就是这个合并过程。一般Reduce是一边copy一边sort，即copy和sort两个阶段是重叠而不是完全分开的。

  当Reduce的输入文件确定，整个Shuffle才最终结束。

参考资料：https://mp.weixin.qq.com/s/6PbnOo3O9qh9zMSgPjdpxw