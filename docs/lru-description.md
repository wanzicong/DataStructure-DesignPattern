### LRU介绍

LRU 是 `Least Recently Used `的简写，字面意思则是最近最少使用。

通常用于缓存的淘汰策略实现，由于缓存的内存非常宝贵，所以需要根据某种规则来剔除数据保证内存不被撑满。


### 简单实现思路(淘汰N之后的LRU数据)

基于双向链表+HashMap实现, 实现的效果:

- 访问某个节点时，将其从原来的位置删除，并重新插入到链表头部。这样就能保证链表尾部存储的就是最近最久未使用的节点，当节点数量大于缓存最大空间时就淘汰链表尾部的节点。

- 为了使删除操作时间复杂度为 O(1)，就不能采用遍历的方式找到某个节点。HashMap 存储着 Key 到节点的映射，通过 Key 就能以 O(1) 的时间得到节点，然后再以 O(1) 的时间将其从双向队列中删除

具体实现代码: [LRU-实现](../src/main/java/com/haobin/algorithm/lru/LRUCache.java)



### 借助LinkedHashMap来实现

JDK容器中，LinkedHashMap的结构也是双向链表，借助内置的容器可以很简单的实现, [LinkedHashMap源码分析](./collection/LinkedHashMap分析.md)

具体实现代码: [LinkedHashMap实现LRU](../src/main/java/com/haobin/algorithm/lru/LRULinkedMap.java)




