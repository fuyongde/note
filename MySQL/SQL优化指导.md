## 6、SQL优化指导

### 6.1、`SELECT`子句优化方法

#### 6.1.1、去掉`SELECT`子句中非必要的结果列

`SELECT *`缺点：

- 造成索引覆盖失效

- 在`filesort`的情况下，若包含`blob`、`text`列，会导致双路排需算法，增加IO

- 返回了一些不需要的列，从而增加了IO、网络传输、CPU开销等

`覆盖索引`是InnoDB中索引的特例，索引中包含查询的所有必填字段，换句话说，索引本身包含执行查询所需的数据，而不必执行额外的读取。

```mysql
-- 如我们需要查询借款详情前1000个借款的订单号，SELECT * 造成覆盖索引失效
SELECT * FROM loan.`t_loan_detail` t WHERE t.`state` = 1 ORDER BY t.`loan_order_no` ASC LIMIT 1000;
-- 若只返回对应的索引列，则查询只需
SELECT t.`loan_order_no` FROM loan.`t_loan_detail` t WHERE t.`state` = 1 ORDER BY t.`loan_order_no` ASC LIMIT 1000;

-- 演示SELECT * 返回所有列与索引列由于IO、网络传输、CPU开销的差异
SELECT * FROM loan.`t_loan_detail` t WHERE t.`id` IN(
366738,356769,218114,264524,237665,245640,32627,417439,19446,1034773,1034848,1131888,353885,626765,595858,458696,1037142,1037161,251055,253267,92924,691318,692113,690894,89507,1143507,956356,976599,428383,567457,201340,753582,205356,1064184,95270,95144,95410,1014538,1017103,1002219,489760,487484,489604,487336,487390,755484,755276,54929,1091330,1083298,1084153,924416,564041,819554,564100,564212,1210389,1210418,931966,687165,413885,415475,35604,159399,119094,425636,1177927,1196009,311368,362388,75902,460079,48395,189470,195147,205678,577364,577392,577670,912445,1184883,1185147,911788,1184694,1184980,911851,657007,529994,719364,794396,437963,272153,530278,437919,330565,601179,330666,330445,14562,183337
);
-- 演示SELECT * 返回所有列与索引列由于IO、网络传输、CPU开销的差异
SELECT t.`id` FROM loan.`t_loan_detail` t WHERE t.`id` IN(
366738,356769,218114,264524,237665,245640,32627,417439,19446,1034773,1034848,1131888,353885,626765,595858,458696,1037142,1037161,251055,253267,92924,691318,692113,690894,89507,1143507,956356,976599,428383,567457,201340,753582,205356,1064184,95270,95144,95410,1014538,1017103,1002219,489760,487484,489604,487336,487390,755484,755276,54929,1091330,1083298,1084153,924416,564041,819554,564100,564212,1210389,1210418,931966,687165,413885,415475,35604,159399,119094,425636,1177927,1196009,311368,362388,75902,460079,48395,189470,195147,205678,577364,577392,577670,912445,1184883,1185147,911788,1184694,1184980,911851,657007,529994,719364,794396,437963,272153,530278,437919,330565,601179,330666,330445,14562,183337
);
```

#### 6.1.2、`SELECT`子句中的聚合函数的优化

```mysql
-- 针对MIN()函数的优化，针对MAX()的同理
EXPLAIN
SELECT MIN(tc.`id`) FROM letou.`t_customer` tc WHERE tc.`name` = '张倩' ORDER BY tc.`id` ASC;

EXPLAIN
SELECT tc.`id` FROM letou.`t_customer` tc WHERE tc.`name` = '张倩' ORDER BY tc.`id` ASC LIMIT 1;
```

#### 6.1.3、`SELECT`子句中加入控制结构避免重复实现优化

```mysql
-- 要查询某各用户新手标和智月升投资次数各是多少
SELECT COUNT(ti.`principal`) FROM letou.`t_invest` ti WHERE ti.`state` = 1 AND ti.`customer_id` = 1478 AND ti.`status` IN (1, 3, 4, 5, 6, 8, 10) AND ti.`product_type` = 1001;
SELECT COUNT(ti.`principal`) FROM letou.`t_invest` ti WHERE ti.`state` = 1 AND ti.`customer_id` = 1478 AND ti.`status` IN (1, 3, 4, 5, 6, 8, 10) AND ti.`product_type` = 2001;

-- 优化之后
SELECT SUM(CASE ti.`product_type` WHEN 1001 THEN 1 ELSE 0 END) AS '新手标', SUM( CASE ti.`product_type` WHEN 2001 THEN 1 ELSE 0 END) AS '智月升' FROM letou.`t_invest` ti WHERE ti.`state` = 1 AND ti.`customer_id` = 1478 AND ti.`status` IN (1, 3, 4, 5, 6, 8, 10);

-- 也可以采用改方式
SELECT ti.`product_type`, COUNT(*) FROM letou.`t_invest` ti WHERE ti.`state` = 1 AND ti.`customer_id` = 1478 AND ti.`status` IN (1, 3, 4, 5, 6, 8, 10) GROUP BY ti.`product_type`;
```

#### 6.1.4、from子句优化方法

##### 6.1.4.1、去掉from子句中未涉及的table及非必要的联接

#### 6.1.5、where子句优化方法

##### 6.1.5.1、index和full table scan的权衡

```mysql
-- 能通过主键索引来取数据的，尽可能通过主键取，避免随机读的问题
SELECT * FROM letou.`t_customer` tc WHERE tc.`id` = 1478;

EXPLAIN
SELECT * FROM letou.`t_pay_record` t ORDER BY t.`id` ASC LIMIT 1000001, 1000;

EXPLAIN
SELECT * FROM letou.`t_pay_record` t WHERE t.`id` > 1015936 ORDER BY t.`id` ASC LIMIT 0, 1000;
```

##### 6.1.5.2、where子句中限制条件的排列与index的命中

```mysql
-- 1.where子句中限制条件排列最好为对应索引的最左前缀(最左原则)
SELECT * FROM letou.`t_interest_water` t WHERE t.`capital_order_no` = '18120439293085060001' AND t.`invest_order_no` = '17120625635152120104';

SELECT * FROM letou.`t_interest_water` t WHERE t.`type` = 4 AND t.`invest_order_no` = '17120625635152120104';
```

##### index merge优化方法

```mysql
-- 索引合并优化后
SELECT * FROM letou.`t_invest` ti WHERE ti.`customer_id` = 1478 OR ti.`order_no` = '17120121000380160103';

-- 优化前
SELECT * FROM letou.`t_invest` ti IGNORE INDEX(`uk_order_no`) WHERE ti.`customer_id` = 1478 OR ti.`order_no` = '17120121000380160103';
```