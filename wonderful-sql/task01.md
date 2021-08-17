# 第一章：初识数据库

**注：Sql的书写规则** 

* win 系统默认不区分表名及字段名的大小写
* **linux / mac 默认严格区分表名及字段名的大小写**

相比`drop / delete`，`truncate`用来清除数据时，速度最快。

**只有未设置 NOT NULL 约束和主键约束的列才可以清空为NULL。**如果将设置了上述约束的列更新为 NULL，就会出错，这点与INSERT 语句相同。

**常见注意事项如下：**

1. MySQL本身不区分大小写，但强烈要求关键字大写，表名、列名用小写；
2. 创建表时，使用统一的、描述性强的字段命名规则保证字段名是独一无二且不是保留字的，不要使用连续的下划线，不用下划线结尾；最好以字母开头
3. 关键字右对齐，且不同层级的用空格或缩进控制，使其区分开，见样例二；
4. 列名少的时候写在一行里无伤大雅；多的时候以及涉及到CASE WHEN 或者聚合计算的时候，建议分行写；个人习惯是逗号在列名前面，方便之后删除某些列，放列名后亦可；
5. 表别名和列别名尽量用有具体含义的词组，不要用a b c，不然以后review的时候会非常痛苦；
6. 运算符前后都加一个空格；
7. 当用到多个表时，请在所有列名前写上引用的表别名，不要嫌麻烦；
8. 每条命令用分号结尾；
9. 养成随手写注释的习惯，注释方法：

```plain
单行注释 #注释文字
单行注释 -- 注释文字
多行注释：/* 注释文字 */
```

索引分类

- 主键索引

建立在主键上的索引被称为主键索引，一张数据表只能有一个主键索引，索引列值不允许有空值，通常在创建表时一起创建。

- 唯一索引

建立在UNIQUE字段上的索引被称为唯一索引，一张表可以有多个唯一索引，索引列值允许为空，列值中出现多个空值不会发生重复冲突。

- 普通索引

建立在普通字段上的索引被称为普通索引。

- 前缀索引

前缀索引是指对字符类型字段的前几个字符或对二进制类型字段的前几个bytes建立的索引，而不是在整个字段上建索引。前缀索引可以建立在类型为char、varchar、binary、varbinary的列上，可以大大减少索引占用的存储空间，也能提升索引的查询效率。

- 全文索引

利用“分词技术”实现在长文本中搜索关键字的一种索引。

语法：`SELECT * FROM article WHERE MATCH (col1，col2，...) AGAINST (expr [ search _ modifier ])`

****

# 练习题

## 1.1

编写一条 CREATE TABLE 语句，用来创建一个包含表 1-A 中所列各项的表 Addressbook （地址簿），并为 regist_no （注册编号）列设置主键约束

表1-A　表 Addressbook （地址簿）中的列

![图片](pic\ch01.04习题1.png)

```sql
use wonderful_sql;
CREATE TABLE Addressbook(
regist_no INT NOT NULL,
name VARCHAR(128) NOT NULL,
address VARCHAR(256) NOT NULL,
tel_no CHAR(10) ,
mail_address CHAR(20) ,
primary key (regist_no)
);
```

## 1.2 

假设在创建练习1.1中的 Addressbook 表时忘记添加如下一列 postal_code （邮政编码）了，请编写 SQL 把此列添加到 Addressbook 表中。

列名        ： postal_code

数据类型 ：定长字符串类型（长度为 8）

约束        ：不能为 NULL

```sql
use wonderful_sql;
ALTER TABLE Addressbook ADD COLUMN postal_code CHAR(8) NOT NULL;
```

## 1.3 填空题

请补充如下 SQL 语句来删除 Addressbook 表。

```sql
DROP table Addressbook;
```

## 1.4 判断题

是否可以编写 SQL 语句来恢复删除掉的 Addressbook 表？

**DROP TABLE 语句执行之后无法恢复。**