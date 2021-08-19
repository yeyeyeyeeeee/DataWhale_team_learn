# **第二章：基础查询与排序**

### 含有NULL时的真值

NULL的真值结果既不为真，也不为假，因为并不知道这样一个值。

那该如何表示呢？

这时真值是除真假之外的第三种值——**不确定**（UNKNOWN）。一般的逻辑运算并不存在这第三种值。SQL 之外的语言也基本上只使用真和假这两种真值。与通常的逻辑运算被称为二值逻辑相对，只有 SQL 中的逻辑运算被称为三值逻辑。

三值逻辑下的AND和OR真值表为：

![图片](https://github.com/PureBuckwheat/DataWhale_team_learn/blob/master/wonderful-sql/pic/ch02.05true3.png)

## 练习题-第一部分

### 2.1

编写一条SQL语句，从 `product`(商品) 表中选取出“登记日期(`regist`)在2009年4月28日之后”的商品，查询结果要包含 `product name` 和 `regist_date` 两列。

```sql
SELECT product_name, regist_date
  FROM product
 WHERE regisyt_date > '2009-04-28'
```

### 2.2

请说出对product 表执行如下3条SELECT语句时的返回结果。

①

```sql
SELECT *
  FROM product
 WHERE purchase_price = NULL;
-- 在商品表中查询所有空值的商品
```

②

```sql
SELECT *
  FROM product
 WHERE purchase_price <> NULL;
-- 在商品表中查询所有不等于空值的商品
```

③

```sql
SELECT *
  FROM product
 WHERE product_name > NULL;
-- 在商品表中查询所有大于空值的商品
```

### 

### 2.3

代码清单2-22（2-2节）中的SELECT语句能够从product表中取出“销售单价（saleprice）比进货单价（purchase price）高出500日元以上”的商品。请写出两条可以得到相同结果的SELECT语句。执行结果如下所示。

```sql
product_name | sale_price | purchase_price 
-------------+------------+------------
T恤衫         | 　 1000    | 500
运动T恤       |    4000    | 2800
高压锅        |    6800    | 5000

SELECT product_name, sale_price, purchase_price
  FROM product
 WHERE sale_price-purchase_price > 500;

SELECT product_name, sale_price, purchase_price
  FROM product
 WHERE purchase_price-sale_price < -500
```

### 

### 2.4

请写出一条SELECT语句，从product表中选取出满足“销售单价打九折之后利润高于100日元的办公用品和厨房用具”条件的记录。查询结果要包括product_name列、product_type列以及销售单价打九折之后的利润（别名设定为profit）。

提示：销售单价打九折，可以通过saleprice列的值乘以0.9获得，利润可以通过该值减去purchase_price列的值获得。

```sql
SELECT product_name, product_type, sale_price*0.9-purchase_price AS "profit"
  FROM product
 WHERE sale_price*0.9-purchase_price;
```

---

## 常用法则

* COUNT函数的结果根据参数的不同而不同。COUNT(*)会得到包含NULL的数据行数，而COUNT(<列名>)会得到NULL之外的数据行数。

* 聚合函数会将NULL排除在外。但COUNT(*)例外，并不会排除NULL。

* MAX/MIN函数几乎适用于所有数据类型的列。SUM/AVG函数只适用于数值类型的列。

* 想要计算值的种类时，可以在COUNT函数的参数中使用DISTINCT。

* 在聚合函数的参数中使用DISTINCT，可以删除重复数据。

  ---

### 聚合键中包含NULL时

将进货单价（purchase_price）作为聚合键举例：

```sql
SELECT purchase_price, COUNT(*)
  FROM product
 GROUP BY purchase_price;
```

此时会将NULL作为一组特殊数据进行处理

---

## 常见错误

在使用聚合函数及GROUP BY子句时，经常出现的错误有：

1. 在聚合函数的SELECT子句中写了聚合健以外的列 使用COUNT等聚合函数时，SELECT子句中如果出现列名，只能是GROUP BY子句中指定的列名（也就是聚合键）。
2. 在GROUP BY子句中使用列的别名 SELECT子句中可以通过AS来指定别名，但在GROUP BY中不能使用别名。因为在DBMS中 ,SELECT子句在GROUP BY子句后执行。
3. 在WHERE中使用聚合函数 原因是聚合函数的使用前提是结果集已经确定，而WHERE还处于确定结果集的过程中，所以相互矛盾会引发错误。 如果想指定条件，可以在SELECT，HAVING（下面马上会讲）以及ORDER BY子句中使用聚合函数。

---

## ORDER BY中列名可使用别名

前文讲GROUP BY中提到，GROUP BY 子句中不能使用SELECT 子句中定义的别名，但是在 ORDER BY 子句中却可以使用别名。为什么在GROUP BY中不可以而在ORDER BY中可以呢？

这是因为SQL在使用 HAVING 子句时 SELECT 语句的顺序为：

FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY。

其中SELECT的执行顺序在 GROUP BY 子句之后，ORDER BY 子句之前。也就是说，当在ORDER BY中使用别名时，已经知道了SELECT设置的别名存在，但是在GROUP BY中使用别名时还不知道别名的存在，所以不能在ORDER BY中可以使用别名，但是在GROUP BY中不能使用别名

---

## 练习题-第二部分

### 2.5

请指出下述SELECT语句中所有的语法错误。

```sql
SELECT product_id, SUM（product_name）
--本SELECT语句中存在错误。
  FROM product 
 GROUP BY product_type 
 WHERE regist_date > '2009-09-01';
-- SUM函数只适用于数值类型的列
-- group by是在 where的基础上对数据进行分组的,应先执行where再执行group by
```

### 2.6

请编写一条SELECT语句，求出销售单价（ `sale_price` 列）合计值大于进货单价（ `purchase_price` 列）合计值1.5倍的商品种类。执行结果如下所示。

```sql
product_type | sum  | sum 
-------------+------+------
衣服         | 5000 | 3300
办公用品      |  600 | 320

SELECT product_type, SUM(sale_price) AS "sum", SUM(purchase_price) AS "sum"
  FROM product
 WHERE SUM(sale_price) > SUM(purchase_price)*1.5;
```

![图片](https://github.com/PureBuckwheat/DataWhale_team_learn/blob/master/wonderful-sql/pic/ch02.08test26.png)

### 2.7

此前我们曾经使用SELECT语句选取出了product（商品）表中的全部记录。当时我们使用了 `ORDER BY` 子句来指定排列顺序，但现在已经无法记起当时如何指定的了。请根据下列执行结果，思考 `ORDER BY` 子句的内容。

![图片](https://github.com/PureBuckwheat/DataWhale_team_learn/blob/master/wonderful-sql/pic/ch02.09test27.png)

```sql
SELECT *
  FROM product
 ORDER BY regist_date DESC;
```

