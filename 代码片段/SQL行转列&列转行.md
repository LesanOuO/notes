# SQL 行转列，列转行

行列转换在做报表分析时还是经常会遇到的，今天就说一下如何实现行列转换吧。

行列转换就是如下图所示两种展示形式的互相转换

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/行转列1.png)

## 行转列

假如我们有下表：

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/行转列2.png)

- 使用PIVOT实现

```sql
SELECT *
FROM student
PIVOT (
    SUM(score) FOR subject IN (语文, 数学, 英语)
)
```

通过上面 SQL 语句即可得到下面的结果

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/行转列3.png)

PIVOT 后跟一个聚合函数来拿到结果，FOR 后面跟的科目是我们要转换的列，这样的话科目中的语文、数学、英语就就被转换为列。IN 后面跟的就是具体的科目值。

- 分组后使用case进行条件判断处理

当然我们也可以用 CASE WHEN 得到同样的结果，就是写起来麻烦一点。

```sql
SELECT name,
  MAX(
  CASE
    WHEN subject='语文'
    THEN score
    ELSE 0
  END) AS "语文",
  MAX(
  CASE
    WHEN subject='数学'
    THEN score
    ELSE 0
  END) AS "数学",
  MAX(
  CASE
    WHEN subject='英语'
    THEN score
    ELSE 0
  END) AS "英语"
FROM student
GROUP BY name
```

使用 CASE WHEN 可以得到和 PIVOT 同样的结果，没有 PIVOT 简单直观。



## 列转行

假设我们有下表 student1

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/行转列3.png)

- 使用UNPIVOT实现

```sql
SELECT *
FROM student1
UNPIVOT (
    score FOR subject IN ("语文","数学","英语")
)
```

通过 UNPIVOT 即可得到如下结果：

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/行转列5.png)

- 分组后使用case进行条件判断处理

我们也可以使用下面方法得到同样结果

```sql
SELECT
    NAME,
    '语文' AS subject ,
    MAX("语文") AS score
FROM student1 GROUP BY NAME
UNION
SELECT
    NAME,
    '数学' AS subject ,
    MAX("数学") AS score
FROM student1 GROUP BY NAME
UNION
SELECT
    NAME,
    '英语' AS subject ,
    MAX("英语") AS score
FROM student1 GROUP BY NAME
```

- UNION & UNION ALL

UNION 操作符用于合并两个或多个 SELECT 语句的结果集。

请注意，UNION 内部的 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每条 SELECT 语句中的列的顺序必须相同。

**SQL UNION 语法**

```sql
SELECT column_name(s) FROM table_name1
UNION
SELECT column_name(s) FROM table_name2
```

**注释：**默认地，UNION 操作符选取不同的值。如果允许重复的值，请使用 UNION ALL。

**SQL UNION ALL 语法**

```sql
SELECT column_name(s) FROM table_name1
UNION ALL
SELECT column_name(s) FROM table_name2
```

另外，UNION 结果集中的列名总是等于 UNION 中第一个 SELECT 语句中的列名。