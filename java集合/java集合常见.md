# Java集合

java集合框架

![71591182958](assets/1715911829587.png)

## List, Set, Queue, Map区别

1.List存储元素有序可重复。

2.Set存储元素无序不可重复。

3.Queue按特定的排序规则确定先后顺序，有序可重复。

4.Map存储的是键值对，key无序不可重复，value无序可重复。一个键只映射到一个值。



## LinkedList 为什么不能实现 RandomAccess 接口？

LinkedList底层的数据结构是链表，内存地址不连续，只能通过指针定位，不支持随机访问。

> RandomAccess(随机访问)指通过索引下标可以快速的访问元素,它是一个标识，来表示实现它的类支持随机访问。



## ArrayList和LinkedList的区别



1.线程安全：ArrayList和LinkedList 都是不同步的，都是线程不安全的。

2.数据结构: ArrayList 是Object[] 数组实现的，而LinkedList 是由双向链表实现的。

3.随机访问：ArrayList底层实现了RandomAccess，支持随机访问，LinkedList 不支持随机访问。

4.内存占用: LinkedList的每个元素占用的内存都比ArrayList大(因为要存储前驱和后继结点的指针),而ArrayList的内存占用体现在列表的结尾会预留一些空间。



