## 执行计划 Explain

`explain`关键字解释了mysql中间的执行优化和过程， 输出的各字段意思：

 - id：	在一个大的查询语句中每个SELECT关键字都对应一个唯一的id
 - select_type：SELECT关键字对应的那个查询的类型
 - table:表名
 - partitions:匹配的分区信息
 - type;针对单表的访问方法
 - possible_keys:可能用到的索引
 - key:实际上使用的索引
 - key_len:实际使用到的索引长度
 - ref:当使用索引列等值查询时，与索引列进行等值匹配的对象信息
 - rows:预估的需要读取的记录条数
 - filtered:某个表经过搜索条件过滤后剩余记录条数的百分比
 - Extra:一些额外的信息
 
### 各字段详解
 
#### id
 
 查询语句中每出现一个`select`关键字，mysql就会为他分配一个唯一的id，一般的查询语句只有一列，包含子查询的就含有多个select
 
 对于连接查询，也会出现多个select列，但是id都是相同的，比如:
 ```
 mysql> EXPLAIN SELECT * FROM s1 INNER JOIN s2;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+---------------------------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                                 |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+---------------------------------------+
|  1 | SIMPLE      | s1    | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 9688 |   100.00 | NULL                                  |
|  1 | SIMPLE      | s2    | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 9954 |   100.00 | Using join buffer (Block Nested Loop) |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+---------------------------------------+
 ```
 
从上述可以看到s1,s2表分别对应一个select，但是id都是1.
在连接查询的执行计划中，每个表都会对应一条记录，这些记录的id列的值是相同的，出现在前边的表表示驱动表，出现在后边的表表示被驱动表
 
 
#### select_type
 
每一个SELECT关键字代表的小查询都定义了一个称之为select_type的属性,描述了这个查询在整个查询中起的作用(union，subquery等)

#### partitions

mysql自带分区功能

#### type
 
 当前查询使用的是什么访问方法(介绍索引的时候，介绍过一些访问方法 ref,ref_or_null,index等)
 
 取值共有:
 - system:当表中只有一条记录
 - const: 主键或者唯一二级索引,并且是等值匹配(只有一行数据)
 - eq_ref:在连接查询时，如果被驱动表是通过主键或者唯一二级索引列等值匹配的方式进行访问的（如果该主键或者唯一二级索引是联合索引的话，所有的索引列都必须进行等值比较），则对该被驱动表的访问方法就是eq_ref。也就是说只在被驱动表读取一行记录
 - ref: 普通索引的等值匹配
 - fulltext: 全文索引
 - ref_or_null: 普通二级索引的等值匹配，并且可以为NULL
 - index_merge: 索引合并的情况
 - unique_subquery：包含`in`的子查询中，可以使用主键进行等值匹配
 - index_subquery： `in`子查询中，使用了普通索引
 - range： 使用索引进行范围查询
 - index：扫描全部索引记录(索引章节介绍过)
 - all： 全表扫描
 
#### possible_keys和key

`possible_keys`列表示在某个查询语句中，对某个表执行单表查询时可能用到的索引有哪些，`key`列表示实际用到的索引有哪些

#### key_len

使用索引的时候，该索引的最大长度。

#### ref

当使用索引列等值匹配的条件去执行查询时，也就是在访问方法是const、eq_ref、ref、ref_or_null、unique_subquery、index_subquery其中之一时，ref列展示的就是与索引列作等值匹配的是个啥，比如只是一个常数或者是某个列

 
#### rows

如果mysql查询器决定使用全表扫描，预计全表扫描的行数

如果mysql决定使用索引扫描，就代表预计扫描索引记录的行数


#### filtered

查询器在扫描的行数中，满足条件查询的行数占比(是个百分比)

#### Extra

一些额外信息