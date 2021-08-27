# 第五章：SQL高级处理

**PARTITON BY** 是用来分组，即选择要看哪个窗口，类似于 GROUP BY 子句的分组功能，但是 PARTITION BY 子句并不具备 GROUP BY 子句的汇总功能，并不会改变原始表中记录的行数。

**ORDER BY** 是用来排序，即决定窗口内，是按那种规则(字段)来排序的。



## **5.1**

请说出针对本章中使用的 product（商品）表执行如下 SELECT 语句所能得到的结果。

```sql
SELECT  product_id
       ,product_name
       ,sale_price
       ,MAX(sale_price) OVER (ORDER BY product_id) AS Current_max_price
  FROM product;
-- 按照product_id进行排序，返回截止到当前行的最高售价
```

## **5.2**

继续使用product表，计算出按照登记日期（regist_date）升序进行排列的各日期的销售单价（sale_price）的总额。排序是需要将登记日期为NULL 的“运动 T 恤”记录排在第 1 位（也就是将其看作比其他日期都早）

```sql
SELECT  product_id
       ,product_name
       ,regist_date
       ,sale_price
       ,sum(sale_price) OVER (ORDER BY regist_date) AS Current_sum_price
  FROM product;
```

## **5.3**

思考题

① 窗口函数不指定PARTITION BY的效果是什么？

② 为什么说窗口函数只能在SELECT子句中使用？实际上，在ORDER BY 子句使用系统并不会报错。

```sql
-- ①不指定窗口，默认窗口为本行及本行以上
-- ②窗口函数是对WHERE和GROUP BY子句处理后的结果进行操作
```

