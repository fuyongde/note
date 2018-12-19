# Explain命令

做SQL优化时，在MySQL数据库中，经常需要先看一条语句的执行计划，通过查看执行计划，有助于对我们的SQL语句以及表结构的一些优化。

用法：

EXPLAIN SQL

example：

`EXPLAIN SELECT * FROM region WHERE id = 110000;`

输出结果：

| id   | select_type | table  | partitions | type  | possible_keys | key     | key_len | ref   | rows | filtered | Extra  |
| ---- | ----------- | ------ | ---------- | ----- | ------------- | ------- | ------- | ----- | ---- | -------- | ------ |
| 1    | SIMPLE      | region | (NULL)     | const | PRIMARY       | PRIMARY | 8       | const | 1    | 100.00   | (NULL) |

输出的每一列的含义：

| column           | main                    |
| ---------------  | ------------------------|
| `id`             | 标识符                   |
| `select_type`    | 查询的类型，有下述选项：<br/>`SIMPLE`：简单的查询，不使用`union`或子查询<br/>`PRIMARY`：最外层的查询<br/>`UNIOS`：UNION中的第二个或后面的SELECT语句<br/>`DEPENDENT UNION`：`UNION`中的第二个或后面的`SELECT`语句，取决于外面的查询<br/>`UNION RESULT`：`UNION`的结果<br/>`SUBQUERY`：子查询中的第一个SELECT<br/>`DEPENDENT SUBQUERY`：子查询中的第一次`SELECT`，取决于外部查询<br/>`DERIVED`：派生的表<br/>`MATERIALIZED`：物化子查询<br/>`UNCACHEABLE SUBQUERY`：无法缓存其结果的子查询，必须为外部查询的每一行重新计算<br/>`UNCACHEABLE UNION`：在属于不可缓存子查询的`UNION`中选择的第二个或更高版本 (请参见`UNCACHEABLE SUBQUERY`) |
| `table`          | 输出的行所引用的表的名称 |
| `partitions`     | 匹配的分区               |
| `type`           | 联接类型，有下述选项：<br/>`system`：该表只有一行（=系统表），这是`const`联接类型的一个特例。<br/>`const`：该表最多只有一个匹配的行, 在查询开始时读取该行。因为只有一行, 所以优化器的其余部分可以将该行中的列中的值视为常量。`const`速度非常快, 因为它们只读取一次。通常查询条件是主键索引会触发该类型。<br/>`eq_ref`：对于上一表中的每个行组合, 从此表中读取一行。除`system`、`const`这是最好的联接类型。可作用于运算符`=`<br/>`ref`：对于上一表中的每个行组合，都将从此表中读取具有匹配索引值的所有行。 可作用于运算符`=`、`<=>`<br/>`fulltext`：全文索引检索，要注意，全文索引的优先级很高，若全文索引和普通索引同时存在时，mysql不管代价，优先选择使用全文索引。<br/>`ref_or_null`：与`ref`方法类似，只是增加了`NULL`值的比较。<br/>`index_merge`：此联接类型表示使用了 "索引合并优化"。在这种情况下, 输出行中的列包含使用的索引列表, 并包含所使用索引的最长键部分的列表。<br/>`unique_subquery`：用于where中的in形式子查询，子查询返回不重复值唯一值。<br/>`index_subquery`：用于in形式子查询使用到了辅助索引或者in常数列表，子查询可能返回重复值，可以使用索引将子查询去重。<br/>`range`：索引范围扫描，常见于使用`=` 、`>`、`<`、`<>`、`>=`、`<=`、`IS NULL`、`BETWEEN`、`IN`、`LIKE`等运算符的查询中。<br/>`index`：索引全表扫描，把索引从头到尾扫一遍，常见于使用索引列就可以处理不需要读取数据文件的查询、可以使用索引排序或者分组的查询。<br/>`all`：全表扫描数据文件，然后再在server层进行过滤返回符合要求的记录。 |
| `possible_keys`  | 可能选择的索引           |
| `key`            | 实际选择的索引           |
| `key_len`        | 所选索引的长度           |
| `ref`            | 与索引相比的列           |
| `rows`           | 扫描的行                 |
| `filtered`       | 按表条件筛选的行的百分比  |
| `Extra`          | 更多信息                 |
