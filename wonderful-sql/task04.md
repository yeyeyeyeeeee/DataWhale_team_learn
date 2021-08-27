

# 第四章：集合运算

连结(JOIN)就是使用某种关联条件(一般是使用相等判断谓词"="), 将其他表中的列添加过来, 进行“添加列”的集合运算. 连结, 能够从两张甚至多张表中获取列, 能够将过去使用关联子查询等过于复杂的查询简化为更加易读的形式, 以及进行一些更加复杂的查询。



关于内连结,需要注意以下三点:

**要点一: 进行连结时需要在 FROM 子句中使用多张表.**

之前的 FROM 子句中只有一张表, 而这次我们同时使用了 ShopProduct 和 Product 两张表,使用关键字 INNER JOIN 就可以将两张表连结在一起了:

```sql
FROM ShopProduct AS SP INNER JOIN Product AS P
```

**要点二:必须使用 ON 子句来指定连结条件.**

在进行内连结时 ON 子句是必不可少的(大家可以试试去掉上述查询的 ON 子句后会有什么结果)。

ON 子句是专门用来指定连结条件的, 我们在上述查询的 ON 之后指定两张表连结所使用的列以及比较条件, 基本上, 它能起到与 WHERE 相同的筛选作用, 我们会在本章的结尾部分进一步探讨这个话题。

**要点三: SELECT 子句中的列最好按照 表名.列名 的格式来使用。**

当两张表的列除了用于关联的列之外, 没有名称相同的列的时候, 也可以不写表名, 但表名使得我们能够在今后的任何时间阅读查询代码的时候, 都能马上看出每一列来自于哪张表, 能够节省我们很多时间。

但是, 如果两张表有其他名称相同的列, 则必须使用上述格式来选择列名, 否则查询语句会报错。

我们回到上述查询所回答的问题. 通过观察上述查询的结果, 我们发现, 这个结果离我们的目标: 找出东京商店的衣服类商品的基础信息已经很接近了. 接下来,我们只需要把这个查询结果作为一张表, 给它增加一个 WHERE 子句来指定筛选条件。

---

**● 外连结要点 1: 选取出单张表中全部的信息**

与内连结的结果相比,不同点显而易见,那就是结果的行数不一样.内连结的结果中有 13 条记录,而外连结的结果中有 15 条记录,增加的 2 条记录到底是什么呢?这正是外连结的关键点. 多出的 2 条记录是高压锅和圆珠笔,这 2 条记录在 ShopProduct 表中并不存在,也就是说,这 2 种商品在任何商店中都没有销售.由于内连结只能选取出同时存在于两张表中的数据,因此只在 Product 表中存在的 2 种商品并没有出现在结果之中.相反,对于外连结来说,只要数据存在于某一张表当中,就能够读取出来.在实际的业务中,例如想要生成固定行数的单据时,就需要使用外连结.如果使用内连结的话,根据 SELECT 语句执行时商店库存状况的不同,结果的行数也会发生改变,生成的单据的版式也会受到影响,而使用外连结能够得到固定行数的结果.虽说如此,那些表中不存在的信息我们还是无法得到,结果中高压锅和圆珠笔的商店编号和商店名称都是 NULL （具体信息大家都不知道,真是无可奈何）.外连结名称的由来也跟 NULL 有关,即“结果中包含原表中不存在（在原表之外）的信息”.相反,只包含表内信息的连结也就被称为内连结了。

**● 外连结要点 2：使用 LEFT、RIGHT 来指定主表.**

外连结还有一点非常重要,那就是要把哪张表作为主表.最终的结果中会包含主表内所有的数据.指定主表的关键字是 LEFT 和 RIGHT.顾名思义,使用 LEFT 时 FROM 子句中写在左侧的表是主表,使用 RIGHT 时右侧的表是主表.代码清单 7-11 中使用了 RIGHT ,因此,右侧的表,也就是 Product 表是主表.我们还可以像代码清单 7-12 这样进行改写,意思完全相同.这样你可能会困惑，到底应该使用 LEFT 还是 RIGHT？其实它们的功能没有任何区别,使用哪一个都可以.通常使用 LEFT 的情况会多一些,但也并没有非使用这个不可的理由,使用 RIGHT 也没有问题。

通过交换两个表的顺序, 同时将 LEFT 更换为 RIGHT(如果原先是 RIGHT,则更换为 LEFT), 两种方式会到完全相同的结果。

---

# **4.1** 

找出 product 和 product2 中售价高于 500 的商品的基本信息。

```sql
SELECT * 
  FROM product 
 WHERE sale_price>500
UNION
SELECT * 
  FROM product2 
 WHERE sale_price>500;
```

# **4.2**

借助对称差的实现方式, 求product和product2的交集。

```sql
SELECT * FROM
(SELECT * FROM product UNION SELECT * FROM product2) AS bingji
WHERE product_id NOT IN
(SELECT product_id FROM
(SELECT * FROM product
WHERE product_id NOT IN (SELECT product_id FROM product2)
UNION
SELECT * FROM product2
WHERE product_id NOT IN (SELECT product_id FROM product))AS duichen);
```

# **4.3**

每类商品中售价最高的商品都在哪些商店有售 ？

```sql
SELECT * FROM
(SELECT product_id,product_name,product_type,MAX(sale_price) FROM product GROUP BY product_type) AS P
LEFT OUTER JOIN shopproduct ON P.product_id=shopproduct.product_id;
SELECT * FROM
(SELECT product_id,product_name,product_type,MAX(sale_price) FROM product GROUP BY product_type) AS P
INNER JOIN shopproduct ON P.product_id=shopproduct.product_id;
```

# **4.4**

分别使用内连结和关联子查询每一类商品中售价最高的商品。

```sql
SELECT p1.product_id, p1.product_name, p1.product_type, p1.sale_price, p2.max_sale_price
FROM product AS p1
INNER JOIN
(SELECT product_type, MAX(sale_price) AS max_sale_price
FROM product
GROUP BY product_type) AS p2
ON p1.product_type = p2.product_type
AND p1.sale_price = p2.max_sale_price;
SELECT p1.product_id, p1.product_name, p1.product_type, p1.sale_price
FROM product AS p1
WHERE p1.sale_price = (SELECT MAX(sale_price) AS max_sale_price
FROM product AS p2
WHERE p1.product_type = p2.product_type
GROUP BY product_type);
```



# **4.5** 

用关联子查询实现：在 product 表中，取出 product_id, produc_name, slae_price, 并按照商品的售价从低到高进行排序、对售价进行累计求和。

```sql
SELECT product_id,product_name,sale_price,
(SELECT SUM(p2.sale_price) FROM product AS p2 WHERE sale_price<=p1.sale_price)
FROM product p1
ORDER BY sale_price;
```



总结：本次任务学习集合运算，主要处理表与表之间的集合关系，双表之间的交集、并集、差等，双表合并以及集合运算中语句的执行顺序。