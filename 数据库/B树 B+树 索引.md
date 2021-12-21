# B树 B+树 索引

## B树

定义：

![image-20211204175310708](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204175310708.png)

**关键字就是指一个节点包含元素的个数。**

![image-20211204175359456](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204175359456.png)

## B+树

![image-20211204205426050](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204205426050.png)

与B树的不同

1. m阶B+树一个节点最多有m个关键字，这点与B树不同。
2. 对于一个节点，有多少关键字，那么就有多少子树。
3. 所有的叶子节点包含了全部关键字及 指向相应记录的指针，而且叶子节点按关键字大小顺序链接。并将所有叶子节点链接起来。

## 数据库索引

![image-20211204213340031](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204213340031.png)

### 计算存放的数据

每一页中有16KB数据，目录中也是16KB数据

![image-20211204213928335](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204213928335.png)

上图中，第一条记录是指最下面的`1 a p`，向上是第二条，再向上是第三条。

### 疑惑

其实我开始有这样的疑惑，我不是根据某个索引查嘛，但我没有创建什么索引啊，后来我才知道原来会根据主键创建（至少我主键是数字的时候是这样的）

![image-20211204220731981](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204220731981.png)

但是，如果我是根据name查，name没有索引，那岂不是一个一个去比较？



> 面试官：既然你提到InnoDB使用的B+ Tree的索引模型，那么你知道为什么采用B+ 树吗？这和Hash索引比较起来有什么优缺点吗？
>
> 我：（突然觉得这道题有点难，但是我还是凭借着自己的知识储备简单的回答上一些）因为Hash索引底层是哈希表，哈希表是一种以key-value存储数据的结构，所以多个数据在存储关系上是完全没有任何顺序关系的，所以，对于区间查询是无法直接通过索引查询的，就需要全表扫描。所以，哈希索引只适用于等值查询的场景。而B+ Tree是一种多路平衡查询树，所以他的节点是天然有序的（[左子节点](https://www.zhihu.com/search?q=左子节点&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A73204847})小于父节点、父节点小于[右子节点](https://www.zhihu.com/search?q=右子节点&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A73204847})），所以对于范围查询的时候不需要做[全表扫描](https://www.zhihu.com/search?q=全表扫描&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A73204847})。
>
> **科普时间：B+ Tree索引和Hash索引区别** [哈希索引](https://www.zhihu.com/search?q=哈希索引&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A73204847})适合等值查询，但是不无法进行范围查询 哈希索引没办法利用索引完成排序 哈希索引不支持多列联合索引的最左匹配规则 如果有大量重复键值得情况下，哈希索引的效率会很低，因为存在哈希碰撞问题



[什么是 MySQL 索引? - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/117419077)

[我以为我对Mysql索引很了解，直到我遇到了阿里的面试官 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/73204847)

**Cardinality 索引基数**

![image-20211204220928949](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204220928949.png)