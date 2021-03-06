# JSON\_VALUE {#concept_62535_zh .concept}

本文为您介绍如何使用实时计算字符串函数JSON\_VALUE。

## 语法 {#section_ckk_stq_dgb .section}

``` {#codeblock_maw_3yj_tsz .language-sql}
VARCHAR JSON_VALUE(VARCHAR content, VARCHAR path)
```

## 入参 {#JSON_VALUE-OG_01 .section}

-   content

    VARCHAR类型, 需要解析的JSON对象，使用字符串表示。

-   path

    VARCHAR类型，解析JSON的路径表达式。 目前pth支持如下表达式。

    |符号|功能|
    |--|--|
    |$|根对象|
    |\[\]|数组下标|
    |\*|数组通配符|
    |.|取子元素|


## 功能描述 {#JSON_VALUE-OG_02 .section}

从JSON字符串中提取指定path的值，不合法的JSON和null都统一返回null。

**说明：** 自定义path需要使用单引号（'），示例如下。

``` {#codeblock_9iv_9ou_u7r}
JSON_VALUE(json,'$.passenger_name') AS ABC
```

## 示例 {#JSON_VALUE-OG_03 .section}

-   测试数据

    |id\(INT\)|json\(VARCHAR\)|path1\(VARCHAR\)|
    |---------|---------------|----------------|
    |1|\[10, 20, \[30, 40\]\]|$\[2\]\[\*\]|
    |2|\{"aaa":"bbb","ccc":\{"ddd":"eee","fff":"ggg","hhh":\["h0","h1","h2"\]\},"iii":"jjj"\}|$.ccc.hhh\[\*\]|
    |3|\{"aaa":"bbb","ccc":\{"ddd":"eee","fff":"ggg",hhh":\["h0","h1","h2"\]\},"iii":"jjj"\}|$.ccc.hhh\[1\]|
    |4|\[10, 20, \[30, 40\]\]|NULL|
    |5|NULL|$\[2\]\[\*\]|
    |6|"\{xx\]"|"$\[2\]\[\*\]"|

-   测试语句

    ``` {#codeblock_d1b_r8i_fgw .language-sql}
    SELECT 
        id，
        JSON_VALUE(json, path1) AS `value`
    FROM 
        T1
    ```

-   测试结果

    |id \(INT\)|value \(VARCHAR\)|
    |----------|-----------------|
    |1|\[30,40\]|
    |2|\["h0","h1","h2"\]|
    |3|H1|
    |4|NULL|
    |5|NULL|
    |6|NULL|


