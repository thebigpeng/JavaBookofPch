# 一：Redis底层数据结构

## 1. 简单动态字符串SDS

SDS即simple dynamic string，redis默认的字符串表示，动态可修改。

SDS结构如下：

- `int len`：记录buf数组中已经使用字节的数量，即SDS保存的字符串长度。
- `int free`：记录buf数组中未使用的字节的数量。
- `char buf[]`：字节数组，用于保存字符串。

[![gk7tWd.png](https://z3.ax1x.com/2021/04/29/gk7tWd.png)](https://imgtu.com/i/gk7tWd)

SDS跟C字符串一样以空字符"\0"结尾，但这1字节的空间不计算在SDS的len属性里面，不需要外部操作。

SDS跟C字符串相比的<font color='red'>优点</font>：

<font color='orange'>优点：</font>

- SDS减少了修改字符串时带来的内存重分配次数，通过未使用空间，SDS实现了<font color='red'>空间预分配</font>和<font color='red'>惰性空间释放</font>两种优化策略。

  1. **空间预分配**：用于优化SDS字符串增长操作，SDS的API在对一个SDS空间进行扩展的时候同时为他分配额外未使用的空间，若对SDS修改后的长度（len值）小于1MB，则预分配跟len值一样大小的空间；若修改后的长度大于1MB，则预分配1MB的未使用空间。
  2. **惰性空间释放**：用于优化SDS的字符串缩短操作，当SDS需要缩短保存的字符串时，不是立即使用内存重分配来回收缩短后多出来的字节，而是使用free属性将这些字节的数量记录起来，并等待将来使用。

  <font color='orange'>通过空间预分配策略redis可以减少连续执行字符串增长操作所需的内存重分配次数。</font>在扩展SDS空间之前首先检查未使用空间是否足够，足够的话优先使用未使用空间，而无需执行内存分配。

  通过惰性空间是否策略，SDS避免了缩短字符串所需的内存重分配操作，并对将来可能的增长操作提供优化。

- SDS是二进制安全的，所有保存的数据都是二进制处理的方式存放，支持任何数据类型存放，<font color='cornflowerblue'>包括空字符"\0"</font>。

- API安全，不会出现缓冲区溢出。

- 获取字符串长度的复杂度为O(1)。

- 可以使用部分C的函数<string.h>。

## 2. 链表

链表可以构成双端链表，adlist.h/listNode结构如下：

[![gkqYYF.png](https://z3.ax1x.com/2021/04/29/gkqYYF.png)](https://imgtu.com/i/gkqYYF)

adlist.h/list结构如下：

[![gkqwO1.png](https://z3.ax1x.com/2021/04/29/gkqwO1.png)](https://imgtu.com/i/gkqwO1)

<font color='orange'>特点</font>：

- 是双端链表
- 无环：表头节点的prev指针和表尾的next指针都指向NULL。
- 带表头指针和表尾指针；
- 带链表长度计数器；
- 多态：链表节点使用`void*`指针来保存节点的值，未指定类型。

## 3. 字典

字典又称符号表(symble table)、关联数组(associate array)或映射(map)，用于保存键值对(key-value pair)的的抽象数据结构。Redis字典使用哈希表作为底层实现，一个哈希表里有多个哈希表节点。

### 3.1 哈希表

哈希表dict.h/dictht结构如下：

[![gkLVn1.png](https://z3.ax1x.com/2021/04/29/gkLVn1.png)](https://imgtu.com/i/gkLVn1)

table属性是一个数组，每个元素都是dict.h/dictEntry结构，如下所示：

[![gkLKhD.png](https://z3.ax1x.com/2021/04/29/gkLKhD.png)](https://imgtu.com/i/gkLKhD)

[![gkjUN6.png](https://z3.ax1x.com/2021/04/29/gkjUN6.png)](https://imgtu.com/i/gkjUN6)

键值对的值可以是一个指针，或者uint64_t整数，或者是一个int64_t整数，<font color='orange'>next属性是指向另一个哈希表节点的指针，可以将多个哈希值相同的键值对连接在一起，来解决键冲突的问题。</font>

[![gkL6H0.png](https://z3.ax1x.com/2021/04/29/gkL6H0.png)](https://imgtu.com/i/gkL6H0)

### 3.2 字典结构

字典dict.h/dic结构如下：

[![gkL5v9.png](https://z3.ax1x.com/2021/04/29/gkL5v9.png)](https://imgtu.com/i/gkL5v9)

type属性和private属性针对不同类型的键值对，为创建多态字典而设置的。

[![gkOSKA.png](https://z3.ax1x.com/2021/04/29/gkOSKA.png)](https://imgtu.com/i/gkOSKA)

`ht`属性包含两个哈希表，一般情况下只使用ht[0]，ht[1]只有在对ht[0]进行rehash的时候才使用。rehashidx记录了rehash目前的进度，如果目前没有进行rehash，则值为1.

### 3.3 哈希算法

字典添加新键值对时，首先根据键计算出哈希值和索引值，再根据索引值把键值对放到哈希表数组的指定索引上，<font color='orange'>Redis使用MurmurHash2算法来计算键的哈希值，使用链地址法来解决哈希冲突</font>。

### 3.4 <font color='orange'>rehash</font>

Redis对哈希表执行rehash的步骤如下：

1. 为字典的ht[1]分配空间，分配的大小取决于要执行的操作以及ht[0]当前包含的键值对数量：
   - <font color='orange'>如果执行扩展操作，ht[1]的大小为第一个大于等于ht[0].used*2的2的次方幂的值；</font>
   - <font color='cornflowerblue'>如果执行的是收缩操作，那么ht[1]的大小等于第一个大于等于ht[0].used的2的次方幂的值</font>。
2. 将保存在ht[0]中的所有键值对rehash到ht[1]上。
3. 当ht[0]上所有的键值对都迁移到了ht[1]上之后（ht[0]变成空表），释放ht[0]，将ht[1]设置为ht[0], 并在ht[1]创建一个空白的哈希表，为下一次rehash做准备。

<font color='red'>自动对哈希表执行扩展操作的条件（任意满足）</font>：

1. 服务器目前没有在执行BGSAVE命令或者BGREWRITEAOF命令，并且哈希表的负载因子大于等于1；
2. 服务器目前正在执行BGSAVE命令或者BGREWRITEAOF命令，并且哈希表的负载因子大于等于5；

<font color='cornflowerblue'>**负载因子**</font>=哈希表已保存的节点数量 / 哈希表的大小：load_factor = ht[0].used / ht[0].size

当负载因子小于0.1时，程序自动对哈希表执行收缩操作。

### 3.5 <font color='orange'>渐进式rehash</font>

rehash时并不是一次性、集中式地完成的，而是分多次、渐进式地完成的。步骤如下：

1. 

## 4. 跳跃表

## 5. 整数集合

## 6. 压缩列表

# 二：Redis对象

Redis基于 前面的数据结构创建了一个对象系统，包括字符串对象、列表对象、哈希对象、集合对象和有序集合对象，还实现了基于引用计数技术的内存回收机制，当程序不用某个对象的时候，这个对象所占用的内存就会被释放。

|            |                |      |
| ---------- | :------------- | ---- |
| 字符串对象 | int,row,embstr |      |
|            |                |      |
|            |                |      |
|            |                |      |

