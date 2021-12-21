单选15题（30分）、填空10题（10分）、判断10题（10分）、简答（25分）、编程（25分）

1. #### Hadoop 常见的发型版本有哪些？

   ![image-20211204141532145](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204141532145.png)

2. #### Hadoop、Spark、Hive、Hbase、Sqoop 这些技术分别是用来做什么的？

   | 技术   | 功能                 |
   | ------ | -------------------- |
   | Hadoop | 分布式离线计算框架   |
   | Spark  | 分布式内存计算框架   |
   | Hive   | HiveQL数据仓库系统   |
   | HBase  | 分布式实时存储数据库 |
   | sqoop  | 数据库间ETL工具      |

3. #### HDFS 元数据是如何存储的？存放在哪里？里面会存放什么内容？在配置集群时是如何通过参数进行配置的？

   元数据分为 **fsimage**  **edits**. 存放在**namenode**中

   ![image-20211204142558449](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204142558449.png)

   ![image-20211204142643058](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204142643058.png)

4. #### HDFS 安全模式有什么特点？

   由于不同节点启动顺序不一样，因此刚启动时会进入安全模式，**只读不能写**。

5. #### HDFS 的设计目标是什么？

   ![image-20211204142901948](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204142901948.png)

6. #### HDFS、Yarn 和 JobHistory 服务的端口分别是多少？如何启动和停止？

   ```shell
   start-dfs.sh
   stop-dfs.sh
   
   start-yarn.sh
   stop-yarn.sh
   
   start-all.sh
   stop-all.sh
   ```

   | 服务名称       | 端口  |
   | -------------- | ----- |
   | HDFS           | 9000  |
   | Yarn           | 8032  |
   | History server | 19888 |

7. #### HDFS 和 Yarn 架构中 Master 和 Slaver 节点上的进程名称是什么？

master：namenode

slaver: datanode

|        | HDFS     | YARN            |
| ------ | -------- | --------------- |
| master | namenode | resourceManager |
| slaver | datanode | NodeManager     |

8. #### HDFS 副本数是否可以修改？如何修改？

配置集群时可以修改  ` dfs.replication`，

![image-20211203174643008](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211203174643008.png)

以及`setrep`命令：

`hdfs -dfs -setrep <copy num> <filename>`

9. #### HDFS 数据块抽象的优点是什么？

![image-20211204143623053](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204143623053.png)

10. #### HDFS三副本放置策略？

![image-20211203175052139](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211203175052139.png)

#### Namenode 的作用是什么？

当客户端向  发送读写请求，首先看有没有权限，再看数据库中是否存在，是管理者的作用。

11. #### HDFS blocksize 和 map任务的数量有什么关系？reduce任务的数量是如何确定的？

HDFS blocksize 默认128M.

**map任务**有1.1倍原则，block没有.

**reduce任务数量**取决于map中**partition数量。**

12. #### HDFS API 读写HDFS文件时如何创建输入输出数据流对象？

    ![image-20211204144333460](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204144333460.png)

    代码

13. #### Hadoop支持的数据压缩算法有哪几种？

![image-20211203175915500](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211203175915500.png)

14. #### 如何自定义序列化类？需要继承什么接口？实现什么方法？

    对象不能直接在网络中传输或者存放到磁盘中，所以需要进行序列化操作。Java序列化包含信息太多不方便，所以需要Hadoop序列化。

    ![image-20211204144712624](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204144712624.png)

![image-20211203180453148](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211203180453148.png)

如果不需要排序，那么只用继承`writable`;如果需要排序，那么需要继承 `WritableComparable`

注意实现对应方法。

15. #### Shuffle过程包括哪四个过程？作用都是什么？它们执行的顺序是什么？（简答）

    map之后，reduce之前。

    map后**写到一个缓冲区中**，达到缓冲区的阈值后，就会**溢写到磁盘**里，如果有分区则写到分区里，并对不同分区里进行**排序**（快排），排序后用**归并算法进行合并**。

![image-20211203180745612](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211203180745612.png)

![image-20211203180931555](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211203180931555.png)

16. #### Shuffle处理全过程如何自定义计数器？

![image-20211204150410277](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204150410277.png)

17. #### Hadoop序列化相对于 Java序列化的优点是什么？

![image-20211203180441266](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211203180441266.png)

18. #### SequenceFile 和 MapFile 类型的区别？

mapfile多了index索引，其中的key是经过排序的；而SequenceFile的key是没有经过排序的。

19. #### TextInputFormat 和 KeyValueTextInputFormat 两种文件输入格式时如何处理key-value的？

TextInputFormat是以文件每一行行首在文件中的**偏移量作为key**，以**一整行作为value**。

KeyValueTextInputFormat默认按制表符将一行分为两列，**第一列为键**，**第二列为值**。

#### PageRank算法中转移矩阵及其迭代计算  你n

#### ![image-20211203181618165](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211203181618165.png)

#### 20. Yarn框架中ResourceManagere的功能是什么？

![image-20211203181846236](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211203181846236.png)





简答题：TOP-N