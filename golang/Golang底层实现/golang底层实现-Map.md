# Golang底层实现系列——map的底层实现

> 本文基于golang 1.14.13



## map的底层数据结构

map的底层实现是一个散列表，map的实现过程实际上就是实现散列表的过程。map主要包含两个结构：`hmap`和`bmap`。

**hmap结构：**

![image-20210525170653056](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/25/170654-597210.jpeg)

**bmap结构：**

![image-20210525172318167](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/25/172318-664306.jpeg)



## map的创建

map的创建通过生成汇编码可以知道，调用的时`runtime.makemap`创建的。

![image-20210521104337068](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/21/104339-958532.jpeg)

*ps:*如果你的map初始容量小于等于8会发现走的是`runtime.fastrand`是因为容量小于8时不需要生成多个桶，一个桶的容量就可以满足（单桶容量通过`bucketCnt`常量定义）。





![image-20210521113121603](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/21/113125-686197.jpeg)



这里主要说明`overLoadFactor`这个计算`B`的函数。

```go
func overLoadFactor(count int, B uint8) bool {
	return count > bucketCnt && uintptr(count) > loadFactorNum*(bucketShift(B)/loadFactorDen)
}
```

参数含义：

count：当前map容量

B：当前B的值

bucketCnt：单个桶数量（默认为8）

loadFactorNum：13

bucketShift : 1<<B

loadFactorDen：2



当`overLoadFactor`返回`true`时则代表需要扩容,`B++`。初始时在知道容量的情况下会连续增加B直到`overLoadFactore`返回`false`。



在申请桶空间时用到的函数是`makeBucketArry`，这个函数会返回指向多个桶空间数组的第一个元素的指针（buckets）和第一个移除桶的指针（nextOverflow）。

![image-20210521151238986](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/21/151241-316554.jpeg)

<center>桶及溢出桶的分布</center>	

如上图（桶及溢出桶的分布图），`makeBucketArry`实际上就是从内存中申请了一个连续的空间内存，内存大小为`bucket.size * buckets总数（基础桶和溢出桶）`，其中前部分是基础桶（baseBucket）然后是溢出桶。



## map的赋值

![image-20210521160137191](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/21/160139-704412.jpeg)

map的赋值会附带着map的扩容和迁移，这两部分是map底层实现的关键，会在后面来单独说，除去这两部分的话，map赋值相对简单，主要是一个hash分为两部分（低位bash和高位hash）低位用于查找bucket，然后tophash快速判断bucket中各个位置是为空。找到key对应的位置，然后设置key及elem（如果key已经存在则会直接返回elem的位置，汇编底层会将值赋值到elem对应位置）。

有一点需要特别注意，bmap中的key/value是如下图这样分布的：

![image-20210521163014299](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/21/163014-522917.jpeg)

其中dataOffset为`bmap`结构的大小。

漏了一个问题：当查找到所有桶发现没有可插入位置时，说明所有的桶都满了，需要申请一个溢出桶（可能是预先申请好的，也可能需要重新申请），并且将溢出桶接到当前bucket的后面（如果已有溢出桶则为最后一个溢出桶的后面）

## map的删除

map的删除调用的时`mapdelete`函数。

![image-20210525120654020](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/25/120705-749772.jpeg)

删除的逻辑相对比较简单，大多函数在赋值操作中已经用到过，所以这里只列除了一些特有的代码（删除数据）。

## map的查询

map的查询调用的是`mapaccess`函数。

![image-20210525180414171](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/25/180416-637117.jpeg)

查询这里主要有一个在扩容过程中查询时需要确认当前key是否已经迁移到新的bucket中。主要是通过`bmap.tophash`来确认。

## map的扩容

map的扩容有量中情况：

第一种：容量不足，“当前容量+1”之后计算出来扩容因子超出了规定值，即`overLoadFactr`函数返回true；

第二种：溢出桶过多。溢出桶数量 ≥ （B&15）。

注：这里的扩容因子的规定值是一个定值，是通过经验得出的结论，我们不做讨论。

当是第一种扩容时，会将map容量扩大一倍。如果是第二种情况则代表可能是空的kv占用的空间过多，这次的扩容不会拓展空间，buckets的数量和原来是一样的但是会对map中的kv进行整理，去除空的kv。

map的扩容只是将底层数组扩大了一倍，并没有进行数据的转移，数据的转移是在扩容后逐步进行的，在迁移的过程中每进行一次赋值（access或者delete）会至少做一次迁移工作。



![image-20210521175439137](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/21/175440-644216.jpeg)



## map的迁移

在map的赋值与删除中我们都有说道迁移，这是扩容后的一部分，迁移的基础结构是`evacDst`数据结构如下：

![image-20210525172805532](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/25/172806-314526.jpeg)

整个迁移的流程如下：

![image-20210525154534661](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/25/154536-196264.jpeg)

![image-20210525164112402](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202105/25/164113-748456.jpeg)

注： 这里只是简单的举例子，实际中hash值不可以小于5，因为小于5的hash都被用于tophash的标识。

## 总结

1. map的赋值难点在于数据的扩容和数据的迁移操作
2. bucket迁移是逐步进行的，在迁移的过程中每进行一次赋值，会做至少一次迁移工作。
3. 扩容不一定会增加空间，也可能只是做了内存整理
4. tophash的标志即可以判断是否为空也可以半段是否搬迁，以及搬迁的位置。
5. 从map中删除key，有可能导致出现很多空的kv，这会导致迁移操作，如果可以避免，尽量避免。



## 参考文章

[深入理解 Go map：赋值和扩容迁移](https://studygolang.com/articles/19219?fr=sidebar)
[Golang map底层实现原理解析](https://blog.csdn.net/luolianxi/article/details/105371079)
[Golang Map 实现](https://www.cnblogs.com/-lee/p/12807063.html)
[golang map的底层实现](https://studygolang.com/articles/27879?fr=sidebar)
[Go语言map底层实现](https://studygolang.com/articles/14436?fr=sidebar)

