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

| 列              | json 名称       | 意义                     |
| --------------- | --------------- | ------------------------|
| `id`            | `select_id`     | 标识符`SELECT`           |
| `select_type`   | `None`          | 的类型`SELECT`           |
| `table`         | `table_name`    | 输出行的表               |
| `partitions`    | `partitions`    | 匹配的分区               |
| `type`          | `access_type`   | 联接类型                 |
| `possible_keys` | `possible_keys` | 可能选择的索引           |
| `key`           | `key`           | 实际选择的索引           |
| `key_len`       | `key_length`    | 所选密钥的长度           |
| `ref`           | `ref`           | 与索引相比的列           |
| `rows`          | `rows`          | 要检查的行的估计         |
| `filtered`      | `filtered`      | 按表条件筛选的行的百分比  |
| `Extra`         | `None`          | 更多信息                 |

select_type的类型及其含义

| `select_type`价值         | json 名称                    | 意义                                                                             |
| ------------------------ | ---------------------------- | -------------------------------------------------------------------------------- |
| `SIMPLE`                 | `None`                       | 简单`选择` (不使用`union`或子查询)                                                 |
| `PRIMARY`                | `None`                       | 最外层`的选择`                                                                     |
| `UNION`                  | `None`                       | `在 union 中`选择第二个或更高版本的`语句`                                           |
| `DEPENDENT UNION`        | `dependent` (`true`)         | `union`中的第二个或更高版本的 `select` 语句, 取决于外部查询                          |
| `UNION RESULT`           | `union_result`               | `union`的结果。                                                                   |
| `SUBQUERY`               | `None`                       | 子查询中的第一次`选择`                                                             |
| `DEPENDENT SUBQUERY`     | `dependent` (`true`)         | 子查询中的第一次`选择` , 取决于外部查询                                              |
| `DERIVED`                | `None`                       | 派生的表                                                                           |
| `MATERIALIZED`           | `materialized_from_subquery` | 物化子查询                                                                         |
| `UNCACHEABLE SUBQUERY`   | `cacheable` (`false`)        | 无法缓存其结果的子查询, 必须为外部查询的每一行重新计算                                 |
| `UNCACHEABLE UNION`      | `cacheable` (`false`)        | 在属于不可缓存子查询的 `union` 中选择的第二个或更高版本 (请参见`UNCACHEABLE SUBQUERY`) |