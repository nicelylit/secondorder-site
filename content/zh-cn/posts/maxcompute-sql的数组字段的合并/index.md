---
title: "MaxCompute SQL的数组字段的合并"
date: 2025-02-09
categories:
  - "engineering"
tags:
  - "technique"
---

在处理字段分组聚合时，如果需要多层次的聚合，有时还需要在新的层次进行该字段的合并，但SQL语言并没有提供直接的语法和简单的函数进行支持，经过`MAP_UNION`的去重操作，结合其他的一些数组转字典的操作，可以实现该功能。具体来说，已知表1中有两行数据

| c1 | c2 | c3 |
| --- | --- | --- |
| s1 | d1 | r1,r2 |
| s1 | d2 | r1,r3 |

怎么得到表2

| c1 | c2 | c3 |
| --- | --- | --- |
| s1 | d1,d2 | r1,r2,r3 |

得到表2的数据，最直接的思路是通过`GROUP BY`进行分组聚合，c2列可以用过`COLLECT_SET`函数外加`ARRAY_JOIN`实现。对于c3列，需要先`SPLIT`成数组，然后再分组合并。查询[MaxCompute SQL的函数手册](https://help.aliyun.com/zh/maxcompute/user-guide/built-in-functions/?spm=a2c4g.11186623.help-menu-27797.d_2_1_3_2.ca5647a5Ye54gn&scm=20140722.H_455448._.OR_help-T_cn~zh-V_1)，在数组方面并没有这样的功能。随后看到`MAP_UNION`的键可以去重，转而想到把数组转换成字典，合并字典的键后，再提取键转为数组，具体的代码可以参考：

```
WITH test_table AS (
    SELECT 's1' AS c1
           ,'d1' AS c2
           ,'r1,r2' AS c3
    UNION
    SELECT 's1' AS c1
           ,'d2' AS c2
           ,'r1,r3' AS c3
)
SELECT c1
       ,ARRAY_JOIN(COLLECT_SET(c2), ',')
       ,ARRAY_JOIN(MAP_KEYS(MAP_UNION(MAP_FROM_ARRAYS(SPLIT(c3, ','), ARRAY_REPEAT(1, SIZE(SPLIT(c3, ',')))))), ',')
FROM test_table
GROUP BY c1
;
```

另外一种思路是设法让表1回到表3的状态，然后再对c3通过`COLLECT_SET`函数外加`ARRAY_JOIN`来实现。

| c1 | c2 | c3 |
| --- | --- | --- |
| s1 | d1 | r1 |
| s1 | d1 | r2 |
| s1 | d2 | r1 |
| s1 | d2 | r3 |

在MaxCompute SQL中，可以用`[LATERAL VIEW](https://help.aliyun.com/zh/maxcompute/user-guide/lateral-view)`语句结合`[EXPLODE](https://help.aliyun.com/zh/maxcompute/user-guide/explode?spm=a2c4g.11186623.help-menu-search-27797.d_0)`函数来实现。具体的代码可以参考：

```
WITH test_table AS
(
    SELECT  's1' AS c1
            ,'d1' AS c2
            ,'r1,r2' AS c3
    UNION
    SELECT  's1' AS c1
            ,'d2' AS c2
            ,'r1,r3' AS c3
)
SELECT  c1
        ,ARRAY_JOIN(COLLECT_SET(c2),',')
        ,ARRAY_JOIN(COLLECT_SET(TRIM(t.c3_item)),',')
FROM    test_table
LATERAL VIEW EXPLODE(SPLIT(c3,',')) t AS c3_item
GROUP BY c1
;
```
