# SQL 索引优化

## 索引的作用

告诉存储引擎如何快速的查找到所需要的数据（相当于书的目录，想找到自己感兴趣的章节可以直接跳到该目录，不然只能一页一页地找）

## INNODB 支持地索引类型

- **Btree 索引**（最广泛）：以 B+ 树的结构存储索引数据
- 自适应 HASH 索引
- 全文索引
- 空间索引

## Btree 索引的特点

- 适用于全值匹配的查询（class_name='mysql'，class_name in ('mysql', 'postgreSQL')）
- 适用于处理范围查找（study_cnt between 1000 and 3000；study_cnt > 3000）
- 从索引的最左侧列开始匹配查找列（create index idx_title_studyCnt on imc_course(title, study_cnt) title='mysql'）

## 应该在什么列上建立索引？

- WHERE 子句中的列；筛选性好的列上（重复少的列）建立索引
- 包含在 ORDER BY、GROUP BY、DISTINCT 中的字段
- 多表 JOIN 的关联列（create INDEX idx_classid_typeid_levelid ON imc_course(class_id,type_id,level_id)，按照可筛选性排序，建立联合索引）

## 如何选择复合索引的顺序？

- 区分度最高的列放在联合索引的最左侧
- 使用最频繁的列放在联合索引的最左侧
- 尽量把字段长度小的列放在联合索引列的最左侧

## Btree 索引的限制

- 只能从最左侧开始按索引键的顺序使用索引，不能跳过索引键
- NOT IN 和 <> 操作无法使用索引
- 索引列上不能使用表达式或者函数

## 索引使用的误区

- :x:索引越多越好
  - 在一定程度是增加查询效率，但同样会降低插入和更新的效率
  - 同样会降低查询效率，因为太多了反而干扰了 MySQL 的执行计划
- :x:使用 IN 列表查询不能用到索引
  - 或运算符可能不行，所以优化方式就是改为 IN
- :x:查询过滤顺序必须同索引键顺序相同才可以使用到索引
  - MySQL 查询优化器会自动调整顺序

## SQL 改写优化（不止是索引）

- 使用 outer join 代替 not in（MySQL 8.0 可以自动做到了）
- 使用 CTE 代替子查询
- 拆分复杂的大 SQL 为多个简单的小 SQL
- 巧用计算列优化查询（`ALTER TABLE imc_classvalue ADD COLUMN total_score DECIMAL(3.1) AS  (content_score+level_score+logic_score)`）

 