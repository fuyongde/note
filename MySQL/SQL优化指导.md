## 6、SQL优化指导

### 6.1、`SELECT`子句优化方法

#### 6.1.1、去掉`SELECT`子句中非必要的结果列

```mysql
-- SELECT * 造成覆盖索引失效
SELECT * FROM loan.`t_loan_detail` t WHERE t.`state` = 1 ORDER BY t.`loan_order_no` ASC LIMIT 1000;

-- 只返回对应的索引列，则
SELECT t.`loan_order_no` FROM loan.`t_loan_detail` t WHERE t.`state` = 1 ORDER BY t.`loan_order_no` ASC LIMIT 1000;

```

#### 6.1.2 `SELECT`子句中的聚合函数的优化

```mysql
-- 针对MIN()函数的优化，针对MAX()的同理
EXPLAIN
SELECT MIN(tc.`id`) FROM letou.`t_customer` tc WHERE tc.`name` = '张倩' ORDER BY tc.`id` ASC;

EXPLAIN
SELECT tc.`id` FROM letou.`t_customer` tc WHERE tc.`name` = '张倩' ORDER BY tc.`id` ASC LIMIT 1;
```

6.1.3、`SELECT`子句中加入控制结构避免重复实现优化

```mysql
-- 要查询某各用户新手标和智月升投资次数各是多少
SELECT COUNT(ti.`principal`) FROM letou.`t_invest` ti WHERE ti.`state` = 1 AND ti.`customer_id` = 1478 AND ti.`status` IN (1, 3, 4, 5, 6, 8, 10) AND ti.`product_type` = 1001;
SELECT COUNT(ti.`principal`) FROM letou.`t_invest` ti WHERE ti.`state` = 1 AND ti.`customer_id` = 1478 AND ti.`status` IN (1, 3, 4, 5, 6, 8, 10) AND ti.`product_type` = 2001;

-- 优化之后
SELECT SUM(CASE ti.`product_type` WHEN 1001 THEN 1 ELSE 0 END) AS '新手标', SUM( CASE ti.`product_type` WHEN 2001 THEN 1 ELSE 0 END) AS '智月升' FROM letou.`t_invest` ti WHERE ti.`state` = 1 AND ti.`customer_id` = 1478 AND ti.`status` IN (1, 3, 4, 5, 6, 8, 10);

-- 也可以采用改方式
SELECT ti.`product_type`, COUNT(*) FROM letou.`t_invest` ti WHERE ti.`state` = 1 AND ti.`customer_id` = 1478 AND ti.`status` IN (1, 3, 4, 5, 6, 8, 10) GROUP BY ti.`product_type`;
```

#### 6.1.4 from子句优化方法

##### 6.1.4.1 去掉from子句中未涉及的table及非必要的联接

#### 6.1.5 where子句优化方法

##### 6.1.5.1 index和full table scan的权衡