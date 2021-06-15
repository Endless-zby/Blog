---
title: MySql序列
updated: 2021-06-13 00:24:14
description: MySql序列
tags:
- mysql
# 置顶优先级 数值越大优先级越高 #
sticky: 9901
# 【可选】文章分类 #
categories: mysql
# 【可选】文章关键字 #
keywords:
- mysql
- 序列
date: 2019-12-13 10:10:21
---

> 虽然mysql天然存在AUTO\_INCREMENT这样的自增长字段 ，但是自增长“序列”和序列是两回事。并且，在实际的项目中显然不是很实用
> 
> 1.MySql中自增长序列的步长只能是1。  
> 2. mysql一个表只能有一个自增长字段。自增长只能被分配给固定表的固定的某一字段，不能被多个表共用。并且只能是数字型。  
> 3. 在历史表和数据迁移时，经常会遇到自增主键重复的问题。
> 4. 自增主键往往是没意义的。 

当然，序列也有缺点，主要就是程序处理麻烦，不如自增方便。oracle的自增有缓存，不用担心效率问题，而mysql只能通过触发器模拟，可能会有性能损失。

##学习创建一个序列

### 首先建立一个序列

``` sql
-- 自增序列创建


SET FOREIGN_KEY_CHECKS=0;


DROP TABLE IF EXISTS `BatchCardPlan`;
CREATE TABLE `BatchCardPlan` (
  `name` varchar(50) CHARACTER SET utf8 COLLATE utf8_bin NOT NULL COMMENT '序列的名字',
  `current_value` bigint(11) NOT NULL COMMENT '序列的当前值',
  `increment` int(11) NOT NULL DEFAULT '1' COMMENT '序列的自增值',
  PRIMARY KEY (`name`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin ROW_FORMAT=DYNAMIC;


-- 序列初始化（初始化序列值为10000000001，步长为1）
INSERT INTO `BatchCardPlan` VALUES ('notis_id', '10000000001', '1');
```

### 序列函数

``` sql
-- 序列生成的函数

-- 查询当前序列值，并将当前序列值 + increment（序列增幅）重新赋值给序列
DELIMITER $$
CREATE  FUNCTION nextval(seq_name varchar(50))
 RETURNS bigint(20)
BEGIN
UPDATE BatchCardPlan
          SET current_value = current_value + increment
          WHERE name = seq_name;
     RETURN currval(seq_name);
END $$
DELIMITER ;

-- 只查询当前序列  不做递增
DELIMITER $$
CREATE  FUNCTION currval(seq_name varchar(50))
 RETURNS bigint(20)
BEGIN
     DECLARE value BIGINT;
     SET value = 0;
     SELECT current_value INTO value
          FROM BatchCardPlan
          WHERE name = seq_name;
     RETURN value;
END $$
DELIMITER ;


-- 初始化序列
DELIMITER $$
CREATE  FUNCTION setval(seq_name varchar(50),value BIGINT)
RETURNS bigint(20)
BEGIN
     UPDATE BatchCardPlan
          SET current_value = value
          WHERE name = seq_name;
     RETURN currval(seq_name);
END$$
DELIMITER ;
```

### 序列方法

```
-- 序列表
SELECT * from BatchCardPlan;

-- 获取BatchCardPlan中notis_id序列当前值 并 对序列 + increment（序列增幅）
select nextval('notis_id')

-- 获取BatchCardPlan中notis_id序列当前值
select currval('notis_id')

-- 初始化BatchCardPlan中notis_id序列
select setval('notis_id',10000000001)
```