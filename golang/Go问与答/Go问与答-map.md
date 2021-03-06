# Go问与答-map

## map底层

### 赋值的时候会触发扩容吗？

答：赋值可能触发扩容。具体是否扩容需要看扩容因子的值和溢出桶数量。

### 负载因子是什么？过高会带来什么问题？它的变动会对哈希表操作带来什么影响吗？

答：负载因子是用于评估哈希表当前的时间复杂度，其与哈希表当前的键值对数、桶数量有关。如果负载因子越大，则说明空间利用率越高，同时产生哈希冲突的可能性也越高。

### 溢出桶越多会带来什么问题？

答：同负载因子一样，会导致hash表的时间复杂度变高。

### 是否要扩容的基准条件是什么？

第一种：容量不足，“当前容量+1”之后计算出来扩容因子超出了规定值，即`overLoadFactr`函数返回true；

第二种：溢出桶过多。溢出桶数量 ≥ （B&15）。

### 扩容的容量规则是怎么样的？

溢出桶过多导致的扩容容量不变；扩容因子导致的扩容则容量翻倍。

### 扩容的步骤是怎么样的？

答：

扩容步骤：

	1. 确定扩容规则
 	2. 初始化、交换新旧桶/溢出桶
 	3. 扩容

### 扩容是一次性扩容还是增量扩容？

答：增量扩容，一次性扩容对于数据量大的map有可能导致卡顿。增量扩容将数据迁移分摊到每次的map操作上，可以避免这个情况的发生。

### 正在扩容的时候又要扩容怎么办？

如果正在扩容，则会不断的进行迁移，等到迁移完成才会进行下一次扩容。

### 扩容时的迁移分流动作是怎么样的？



### 在扩容动作中，底层汇编承担了什么角色？做了什么事？



### 在 buckets/overflow buckets 中寻找时，是如何 “快速” 定位值的？低八位、高八位的用途？

答：根据key生成16位hash。低八位用于查找所在的bucket，高八位用于快速过滤bucket中的key。

### 空槽有可能出现在任意位置吗？假设已经没有空槽了，但是又有新值要插入，底层会怎么处理

答：可能。如果满足了扩容条件会进行扩容，如果不满足，则会申请溢出桶。

