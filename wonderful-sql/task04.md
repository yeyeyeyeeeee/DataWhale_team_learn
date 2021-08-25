

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



