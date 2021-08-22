# 第三章 复杂一点的查询

《*sql**基础**教程**第2版*》用一句话非常凝练的概括了视图与表的区别---“是否保存了实际的数据”。

所以视图并不是数据库真实存储的数据表，它可以看作是一个窗口，通过这个窗口我们可以看到数据库表中真实存在的数据。

所以要区别视图和数据表的本质，即视图是基于真实表的一张虚拟的表，其数据来源均建立在真实表的基础上。

**“视图不是表，视图是虚表，视图依赖于表”。**

---

**为什么会存在视图?**

那既然已经有数据表了，为什么还需要视图呢？主要有以下几点原因：

1. 通过定义视图可以将频繁使用的SELECT语句保存以提高效率。
2. 通过定义视图可以使用户看到的数据更加清晰。
3. 通过定义视图可以不对外公开数据表全部字段，增强数据的保密性。
4. 通过定义视图可以降低数据的冗余。

---

## 如何更新视图内容

因为视图是一个虚拟表，所以对视图的操作就是对底层基础表的操作，所以在修改时只有满足底层基本表的定义才能成功修改。

对于一个视图来说，如果包含以下结构的任意一种都是不可以被更新的：

* 聚合函数 SUM()、MIN()、MAX()、COUNT() 等。
* DISTINCT 关键字。
* GROUP BY 子句。
* HAVING 子句。
* UNION 或 UNION ALL 运算符。
* FROM 子句中包含多个表。

视图归根结底还是从表派生出来的，因此，如果原表可以更新，那么 视图中的数据也可以更新。反之亦然，如果视图发生了改变，而原表没有进行相应更新的话，就无法保证数据的一致性了。

* 更新视图

因为我们刚刚修改的productSum视图不包括以上的限制条件，我们来尝试更新一下视图

```sql
UPDATE productsum
   SET sale_price = '5000'
 WHERE product_type = '办公用品';
```

此时我们再查看productSum视图，可以发现数据已经更新了

![图片](.\pic\ch03.07productsum2.png)

此时观察原表也可以发现数据也被更新了

![图片](.\pic\ch03.08productsumsrc.png)

不知道大家看到这个结果会不会有疑问，刚才修改视图的时候是设置product_type='办公用品'的商品的sale_price=5000，为什么原表的数据只有一条做了修改呢？

还是因为视图的定义，视图只是原表的一个窗口，所以它修改也只能修改透过窗口能看到的内容。

**注意：这里虽然修改成功了，但是并不推荐这种使用方式。而且我们在创建视图时也尽量使用限制不允许通过视图来修改表**

---

## 3.1

创建出满足下述三个条件的视图（视图名称为 ViewPractice5_1）。使用 product（商品）表作为参照表，假设表中包含初始状态的 8 行数据。

* 条件 1：销售单价大于等于 1000 日元。
* 条件 2：登记日期是 2009 年 9 月 20 日。
* 条件 3：包含商品名称、销售单价和登记日期三列。

对该视图执行 SELECT 语句的结果如下所示。

```sql
-- 商品表
CREATE TABLE product
(product_id CHAR(4) NOT NULL,
 product_name VARCHAR(100) NOT NULL,
 product_type VARCHAR(32) NOT NULL,
 sale_price INTEGER ,
 purchase_price INTEGER ,
 regist_date DATE ,
 PRIMARY KEY (product_id));
```

```sql
-- 创建视图
CREATE VIEW ViewPractice5_1 (product_name, sale_price, regist_date)
AS
SELECT product_name, sale_price, regist_date
  FROM product
 WHERE sale_price >= 1000 and regist_date = '2009-9-20';
```



```sql
SELECT * FROM ViewPractice5_1;
```

执行结果

```sql
product_name | sale_price | regist_date
--------------+------------+------------
T恤衫         | 　 1000    | 2009-09-20
菜刀          |    3000    | 2009-09-20
```

## 3.2

向习题一中创建的视图 `ViewPractice5_1` 中插入如下数据，会得到什么样的结果？

```sql
INSERT INTO ViewPractice5_1 VALUES (' 刀子 ', 300, '2009-11-02');
```

```sql
-- 得到如下视图
product_name | sale_price | regist_date
--------------+------------+------------
T恤衫         | 　 1000    | 2009-09-20
菜刀          |    3000    | 2009-09-20
刀子          |    300     | 2009-11-02
```



## 3.3

请根据如下结果编写 SELECT 语句，其中 sale_price_all 列为全部商品的平均销售单价。

```sql
product_id | product_name | product_type | sale_price | sale_price_all
------------+-------------+--------------+------------+---------------------
0001       | T恤衫         | 衣服         | 1000       | 2097.5000000000000000
0002       | 打孔器        | 办公用品      | 500        | 2097.5000000000000000
0003       | 运动T恤       | 衣服          | 4000      | 2097.5000000000000000
0004       | 菜刀          | 厨房用具      | 3000       | 2097.5000000000000000
0005       | 高压锅        | 厨房用具      | 6800       | 2097.5000000000000000
0006       | 叉子          | 厨房用具      | 500        | 2097.5000000000000000
0007       | 擦菜板        | 厨房用具       | 880       | 2097.5000000000000000
0008       | 圆珠笔        | 办公用品       | 100       | 2097.5000000000000000
```

```sql
SELECT product_id, product_name, product_type, sale_price , (SELECT AVG(sale_price) FROM product) AS sale_price_all
  FROM product;
```



## 3.4

请根据习题一中的条件编写一条 SQL 语句，创建一幅包含如下数据的视图（名称为AvgPriceByType）。

```sql
product_id | product_name | product_type | sale_price | avg_sale_price
------------+-------------+--------------+------------+---------------------
0001       | T恤衫         | 衣服         | 1000       |2500.0000000000000000
0002       | 打孔器         | 办公用品     | 500        | 300.0000000000000000
0003       | 运动T恤        | 衣服        | 4000        |2500.0000000000000000
0004       | 菜刀          | 厨房用具      | 3000        |2795.0000000000000000
0005       | 高压锅         | 厨房用具     | 6800        |2795.0000000000000000
0006       | 叉子          | 厨房用具      | 500         |2795.0000000000000000
0007       | 擦菜板         | 厨房用具     | 880         |2795.0000000000000000
0008       | 圆珠笔         | 办公用品     | 100         | 300.0000000000000000
```

提示：其中的关键是 `avg_sale_price` 列。与习题三不同，这里需要计算出的 是各商品种类的平均销售单价。这与使用关联子查询所得到的结果相同。 也就是说，该列可以使用关联子查询进行创建。问题就是应该在什么地方使用这个关联子查询。

```sql
-- 创建视图
CREATE VIEW AvgPriceByType (product_id, product_name, product_type, sale_price, avg_sale_price)
AS
SELECT product_id, product_name, product_type, sale_price, 
(SELECT AVG(sale_price)
  FROM product AS p2
 WHERE p1.product_type = p2.product_type
 GROUP BY p2.product_type
)
  FROM product AS p1;
```

#

## 3.5 判断题

运算或者函数中含有 NULL 时，结果是否都会变为NULL ？

**是的，需要使用coalesce函数**

## 3.6

对本章中使用的 `product`（商品）表执行如下 2 条 `SELECT` 语句，能够得到什么样的结果呢？

①

```sql
SELECT product_name, purchase_price
  FROM product
 WHERE purchase_price NOT IN (500, 2800, 5000);
```

```sql
-- 全部进价不是500、2800、5000的记录，选取其名称和进价
-- 进价为null的记录不选取，in和not in都不能选取null
```

②

```sql
SELECT product_name, purchase_price
  FROM product
 WHERE purchase_price NOT IN (500, 2800, 5000, NULL);
```

```sql
-- 不能取出任何记录
-- 理由:
-- not in的参数中不能包含null，否则结果为空
-- 使用子查询作为not in的参数时，子查询的返回值也不能为null
```



## 3.7

按照销售单价( `sale_price` )对练习 3.6 中的 `product`（商品）表中的商品进行如下分类。

* 低档商品：销售单价在1000日元以下（T恤衫、办公用品、叉子、擦菜板、 圆珠笔）
* 中档商品：销售单价在1001日元以上3000日元以下（菜刀）
* 高档商品：销售单价在3001日元以上（运动T恤、高压锅）

请编写出统计上述商品种类中所包含的商品数量的 SELECT 语句，结果如下所示。

执行结果

```sql
low_price | mid_price | high_price
----------+-----------+------------
        5 |         1 |         2
```

```sql
SELECT 
SUM(
    CASE WHEN sale_price <= 1000
         	THEN 1
         	ELSE 0
         END
   ) AS low_price,
SUM(
    CASE WHEN sale_price BETWEEN 1001 AND 3000
             THEN 1
             ELSE 0
         END
   ) AS mid_price,
SUM(
    CASE WHEN sale_price >= 3001
            THEN 1
            ELSE 0
         END
   ) AS high_price
FROM product;
```



总结：本期组队学习拖延症，周六日做five太开心，忘记搬砖，可耻。学习了视图、子查询、许多函数的使用、谓词、case表达式，熟练掌握还需主动刷题。

