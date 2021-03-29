...menustart

- [Mysql实践](#dc994915bbbd438c2b523c6b1e4008ba)
- [运维](#60eec86da0dbf4c99b14a66d4a37c1e3)
    - [mysql 慢日志查询](#1838954ac65225f29f4cccf9131bb24f)
    - [mysql 操作记录查询](#e1a8175ef9a04770289a68720bff0ffc)
    - [mysql 记录 未使用 index的查询](#06380fb607958af6dbecb617ce31f2cd)
    - [how to check whether mysql reuse the connection](#005022c3c2f0c952bbd1532235bc4959)
    - [restore database from dump file](#c4606a5312075cb8424b31a364e46848)
    - [Create a `new_user` and Grant all privileges on db `db_test`](#c3b5d31eac469e51b08ec13a8edc866e)
- [Query Tips](#1eeba8438a86727ddaeaaac5710a83ff)
    - [`select count(*)` is very slow on large table](#c4935fa15c0a1305da238eec81cc54b3)
    - [`select ... limit offset , n ` is slow when offset  is higher](#5c7924ade1a946ba9a0af0cc562c127b)
    - [use COALESCE to return a default value if select get no result](#471e27dbca2b606ef2e3110b2bc36fcc)
    - [Sub Query example](#a21afd692dc65c7ff60c8f549b7f1f5a)
    - [CASE ... WHEN ... ELSE... END](#cba58cab71d7e6df49942060252f546d)
    - [Convert a timestamp to seconds(GMT)](#551a3f8ce4409963ec6de228ccc44ae0)
    - [三元表达式](#6ada22780ed552c34465864a2648f7e9)
    - [Update RANK while doing query](#9b22da090fcab767f2930f6e0b9b3251)

...menuend


<h2 id="dc994915bbbd438c2b523c6b1e4008ba"></h2>


# Mysql实践

<h2 id="60eec86da0dbf4c99b14a66d4a37c1e3"></h2>


# 运维

<h2 id="1838954ac65225f29f4cccf9131bb24f"></h2>


## mysql 慢日志查询

```
set global slow_query_log='ON';
set global long_query_time=1;

show variables like 'slow_query%';
show variables like 'long_query_time';
```
- Now check the slow logs at `mysql.slow_log`

<h2 id="e1a8175ef9a04770289a68720bff0ffc"></h2>


## mysql 操作记录查询

```
SET GLOBAL general_log = 'ON'
SET GLOBAL log_output = 'TABLE'

show variables like '%general%';
```


- Now you can find the mysql operation log in `mysql.general_log` table


<h2 id="06380fb607958af6dbecb617ce31f2cd"></h2>


## mysql 记录 未使用 index的查询

```
set global log_queries_not_using_indexes=ON;
```

- those queries will appears in slow_log

<h2 id="005022c3c2f0c952bbd1532235bc4959"></h2>


## how to check whether mysql reuse the connection

- in `mysql.general_log` ,  if the connection is reused, you should see the `connection` event only at the very beginning


<h2 id="c4606a5312075cb8424b31a364e46848"></h2>


## restore database from dump file

```
mysql -uroot -ppwd  < dumpfile
```

<h2 id="c3b5d31eac469e51b08ec13a8edc866e"></h2>


## Create a `new_user` and Grant all privileges on db `db_test`

```mysql
CREATE USER 'new_user'@'%' IDENTIFIED BY 'new_user_pwd';
GRANT ALL PRIVILEGES  ON db_test.* TO 'new_user'@'%' WITH GRANT OPTION;
```


<h2 id="1eeba8438a86727ddaeaaac5710a83ff"></h2>


# Query Tips

<h2 id="c4935fa15c0a1305da238eec81cc54b3"></h2>


## `select count(*)` is very slow on large table

- Here's a cheap way to get an estimated row count:

```
> select TABLE_ROWS FROM INFORMATION_SCHEMA.TABLES where TABLE_SCHEMA='your_db_name' and TABLE_NAME='your_table_name';
+------------+
| TABLE_ROWS |
+------------+
|      57889 |
+------------+
1 rows in set (0.00 sec)
``` 

<h2 id="5c7924ade1a946ba9a0af0cc562c127b"></h2>


## `select ... limit offset , n ` is slow when offset  is higher

- `limit offset ,n ` -> `where id > offset limit n` 
- Note:
    - `id` is the auto incr primary key , and should be continous (that is , you should NOT delete the rows )


<h2 id="471e27dbca2b606ef2e3110b2bc36fcc"></h2>


## return a default value if select get no result

1. COALESCE returns 1st non-NULL value
    ```mysql
    select COALESCE(AVG(distance), 0) as baseline from records where uuid=?
    ```
2. use UNION
    ```mysql
    (select value from conf where entry = ? for update ) UNION (SELECT '' )
    ```
    - replace the value `''` in right-hand select with your expected value.

<h2 id="a21afd692dc65c7ff60c8f549b7f1f5a"></h2>


## Sub Query example

```mysql
select COALESCE( sum(orderAmount), 0 )  from payment_vivosdk   
where 
    uuid="test-User-0" and 
    paidtime >= ( select COALESCE( AVG(startTime), -1) from tbl_rechargeAcc where activity_kind = 2 and endTime > 969393337  ) and 
    paidtime <= ( select COALESCE( AVG(deadline),  -1) from tbl_rechargeAcc where activity_kind = 2 and endTime > 969393337  ) 
```

<h2 id="cba58cab71d7e6df49942060252f546d"></h2>


## CASE ... WHEN ... ELSE... END

```mysql
    WHERE m.p1 = ? OR m.p2 = ?  ORDER by   CASE 
        when (m.p1=?) and ((withdraw&1)<>0) then match_id - 10000000
        when (m.p2=?) and ((withdraw&2)<>0) then match_id - 10000000
        ELSE  match_id END   DESC LIMIT 20
```



<h2 id="551a3f8ce4409963ec6de228ccc44ae0"></h2>


## Convert a timestamp to seconds(GMT)

不同时区的数据库，存放的date 受时区影响, UNIX_TIMESTAMP方法不是我们想要的...

```mysql
SELECT TIMESTAMPDIFF( SECOND, "1970-01-01 00:00:00" , "2020-05-01 12:05:55" );
```

<h2 id="6ada22780ed552c34465864a2648f7e9"></h2>


## 三元表达式

```mysql
... status=if(status="created","paid",status)
```

<h2 id="9b22da090fcab767f2930f6e0b9b3251"></h2>


## Update RANK while doing query

- query , and return rank in query
    ```mysql
    select @rownum:=@rownum+1 as 'rank', uuid, rank_score
    from pvp_hsw, 
    (SELECT @rownum:=0) as r  -- Every derived table must have its own alias
    order by rank_score desc;
    ```
    - here `@rownum` will safely reset everytime

- update rank info while doing query
    ```mysql
    SET @rownum=0;
    ```
    ```mysql
    update pvp_hsw as p set week_rank=@rownum:=@rownum+1 order by rank_score desc;
    ```
    - OR in one line...
        ```mysql
        update pvp_hsw  set week_rank=@rownum:=@rownum+1   order by rank_score desc , (SELECT @rownum:=0)  ;
        ```







