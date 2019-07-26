
<center><font size=6>**MYSQL知识点汇总**</font></center>

## mysql的基础安装和高阶配置

### install on ubuntu

 >  1.每个文件只包含一个React组件，命名要顾名知义
 
 >  2.尽可能的使用ES6语法
 
 >  3.console，alert在调试时写，调试完立即删除
 
 > 4.代码缩进格式统一，工具类方法注意添加注释
 

## mysql权限体系原理
  > 1.命名中不要出现中文

  > 2.文件名: 文件夹和文件名要使用大驼峰命名法,比如 HomeIndex.js
  
  > 3.图片命名规则： 模块_xx.png 或者 模块/xx.png
  
 ```
  setting_bg.png
  setting/bg.png

 ```

  > 4 组件命名: 与文件名（除中缀外）完全一致。如果组件单独放置在目录中，则目录名也一致。
  
```
  import FootView from './FootView'

```


## sql基础语法入门与实践
### 变量

>	1.尽量使用let来代替var

>	2.对于全局变量声明，采用`global.xxx = xxx`，但应避免声明过多全局变量污染环境

### 常量
  > 1.对于常量应使用const进行声明，命名采用驼峰写法
  
  > 2.对于使用 immutable(不可改变的) 数据应用const进行声明

### 字符串
> 1.以反引号( ` )标示
> 2.可读性更强，代码更易编写

```
   `How are you, ${name}?`
```

## sql编程高级进阶
### 属性名
 
> 1.组件的属性使用小驼峰命名法

 ```
  fontSize={12}, textColor='#FFFFFF' 
 ```
> 2.使用外联样式时,属性名最好带有Style关键字
  
  ```
  flexStyle.js
  ```


## sql 优化

### 查看sql可优化的语句

* explain

> Explain 命令在解决数据库性能上是第一推荐使用命令， 大部分的性能问题都可以通过此命令来简单的解决，Explain可以用来查看sql语句的执行效果，可以帮助选择更好的索引和优化查询语句。

> Explain 语法：  explain select ... from .. [where ...]

> 输出 

    +----+-------------+-------+-------+-------------------+---------+---------+-------+------
    | id | select_type | table | type | possible_keys | key | key_len | ref | rows | Extra |
    +----+-------------+-------+-------+-------------------+---------+---------+-------+------
   
* id 
    sql的执行序列号，相同的，从上而下执行，

* select_type: select的类型，可以有以下几中

```

SIMPLE：简单SELECT(不使用UNION或子查询等)
 
PRIMARY：最外面的SELECT
 
UNION：UNION中的第二个或后面的SELECT语句
 
DEPENDENT UNION：UNION中的第二个或后面的SELECT语句，取决于外面的查询
 
UNION RESULT：UNION的结果。
 
SUBQUERY：子查询中的第一个SELECT
 
DEPENDENT SUBQUERY：子查询中的第一个SELECT，取决于外面的查询
 
DERIVED：导出表的SELECT(FROM子句的子查询)

```

* table: 执行的是那张表

* type: 这列最重要，显示了连接使用了哪种类别，有无使用索引，是使用explain命令分析性能瓶颈的关键项之一

```
结果值从好到坏依次是：
 
system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL
 
一般来说，得保证查询至少达到range级别，最好能达到ref，否则就可能会出现性能问题。
 
 
•system: const 的一种特例，表中只有一行数据
•const: 当确定最多只会有一行匹配的时候，MySQL优化器会在查询前读取它而且只读取一次，因此非常快。使用主键查询往往就是 const 级别的，非常高效
•eq_ref: 最多只返回一条符合条件的记录，通过使用在两个表有关联字段的时候
•ref: 通过普通索引查询匹配的很多行时的类型
•fulltext: 全文索引
•ref_or_null: 跟 ref 类似的效果，不过多一个列不能 null 的条件
•index_merge: 此连接类型表示使用了索引合并优化。在这种情况下，输出行中的 key 列包含使用的索引列表，key_len包含所用索引的最长 key 部分列表
•unique_subquery: 在使用 in 查询的情况下会取代 eq_ref
•range: 范围扫描，一个有限制的索引扫描。key 列显示使用了哪个索引。当使用=、 <>、>、>=、<、<=、IS NULL、<=>、BETWEEN 或者 IN 操作符，用常量比较关键字列时,可以使用 range
•index: 类似全表扫描，只是扫描表的时候按照索引次序进行而不是行。主要优点就是避免了排序, 但是开销仍然非常大。如在Extra列看到Using index，说明正在使用覆盖索引，只扫描索引的数据，它比按索引次序全表扫描的开销要小很多
•ALL: 全表扫描

```

5、possible_keys：列指出MySQL能使用哪个索引在该表中找到行

6、key：显示MySQL实际决定使用的键（索引）。如果没有选择索引，键是NULL

7、key_len：显示MySQL决定使用的键长度。如果键是NULL，则长度为NULL。使用的索引的长度。在不损失精确性的情况下，长度越短越好

8、ref：显示使用哪个列或常数与key一起从表中选择行。

9、rows：显示MySQL认为它执行查询时必须检查的行数。

10、Extra：包含MySQL解决查询的详细信息，也是关键参考项之一。

```
Distinct
一旦MYSQL找到了与行相联合匹配的行，就不再搜索了
 
Not exists
MYSQL 优化了LEFT JOIN，一旦它找到了匹配LEFT JOIN标准的行，
 
就不再搜索了
 
Range checked for each
 
Record（index map:#）
没有找到理想的索引，因此对于从前面表中来的每一 个行组合，MYSQL检查使用哪个索引，并用它来从表中返回行。这是使用索引的最慢的连接之一
 
Using filesort
看 到这个的时候，查询就需要优化了。MYSQL需要进行额外的步骤来发现如何对返回的行排序。它根据连接类型以及存储排序键值和匹配条件的全部行的行指针来 排序全部行
 
Using index
列数据是从仅仅使用了索引中的信息而没有读取实际的行动的表返回的，这发生在对表 的全部的请求列都是同一个索引的部分的时候
 
Using temporary
看到这个的时候，查询需要优化了。这 里，MYSQL需要创建一个临时表来存储结果，这通常发生在对不同的列集进行ORDER BY上，而不是GROUP BY上
 
Using where
使用了WHERE从句来限制哪些行将与下一张表匹配或者是返回给用户。如果不想返回表中的全部行，并且连接类型ALL或index， 这就会发生，或者是查询有问题

```