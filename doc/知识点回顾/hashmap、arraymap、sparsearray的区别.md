## Android知识点回顾-HashMap/ArrayMap/SparseArray

#### 1. 内部结构
hashmap内部是一个容量为16的数组，这个数组保存的是一个链表的头结点。

arraymap内部是有两个数组，mHashes保存hashcode，mArrays保存key，value. mArrays保存数据是先保存key，然后保存key对应的value，然后再继续下一个key-value,所以marrays的长度是mhashes的2倍

sparsearray相当于简化版的arraymap，只能保存key为int的数据。其中mhashes保存key，marrays保存value。

#### 2. 性能 

##### 时间效率
因为hashmap是数组加链表的结构，所以不管是查询还是插入都很快

arraymap和sparsearry因为内部只有数组并且用到2分法，所以查询很快，但是插入就比较慢了

##### 空间效率
对内存的使用来说arramymap占用的内存很小，hashmap占用的内存较大

