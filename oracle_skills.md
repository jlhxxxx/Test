# oracle奇技淫巧

## select 1 from dual

* 用PLSQL执行数据查询的时候，FROM子句是必须的，当不想查具体的表时，可以使用虚拟表Dual。

  ```sql
  select 1+1 from dual;
  select 1 from dual where null is null;
  select 'success' as result from dual where 50<60;
  select sysdate from dual;
  ```

## case语句

参考文章：[Oracle CASE WHEN 用法介绍](http://www.cnblogs.com/soundcode/p/5549901.html) 

* case表达式有两种形式

  ```sql
  --简单Case函数  
  CASE aac004  
  WHEN '1' THEN 'male'  
  WHEN '2' THEN 'female'  
  ELSE null END 

  --Case搜索函数  
  CASE
  WHEN aac004 = '1' THEN 'male'  
  WHEN aac004 = '2' THEN 'female'  
  ELSE null END
  ```

  ​


* SELECT CASE WHEN 用法

  ```sql
  SELECT  aac012, COUNT (CASE aac004
                         WHEN '1' THEN 1
                         ELSE NULL
                         END) as male,
                  COUNT (CASE aac004
                         WHEN '2' THEN 1
                         ELSE NULL
                         END) as female
      FROM ac01 where aac002 like '%19940911%' GROUP BY aac012;
  ```

  ```sql
  select (case 
          when 表达式1 then 'result1'
          when 表达式2 then 'result2'
          else 'result3'
          end) as result from dual;
  ```

* WHERE CASE WHEN 用法

  ```sql
  SELECT T2.*, T1.*
     FROM T1, T2
    WHERE (CASE 
           WHEN T2.COMPARE_TYPE = 'A' AND T1.SOME_TYPE LIKE 'NOTHING%' THEN 1
           WHEN T2.COMPARE_TYPE != 'A' AND T1.SOME_TYPE NOT LIKE 'NOTHING%' THEN 1
           ELSE 0
           END) = 1;
  ```

* GROUP BY CASE WHEN 用法

  ```sql
  select case aac004
  	when '1' then 'male'
  	when '2' then 'female'
  	else null
  	end sex,--别名
  	count(*)
  	from ac01 where aac002 like '%19940911%'
  	group by case aac004
  		when '1' then 'male'
  		when '2' then 'female'
  		else null end;
  ```

## decode用法

* 等价于if-then-else

  ```sql
  DECODE(value, if1, then1, if2,then2, if3,then3, . . . else )
  ```

* 举例

  ```sql
  --salary>8000,加薪15%；salary<8000,加薪20%;salary=8000,加薪15%
  select decode(sign(salary - 8000),1,salary*1.15,-1,salary*1.2,salary*1.15) from employee；
  --统计ac01表中19940911出生的男女数
  select count(decode(aac004,'1',1,null)) as male,count(decode(aac004,'2',1,null)) as female from ac01 where aac002 like '%19940911%'；
  --另外一种实现方法
  select decode(aac004,'1','male','2','female',null) sex,count(*) from ac01 where aac002 like '%19940911%' group by decode(aac004,'1','male','2','female',null)；
  ```

  ​