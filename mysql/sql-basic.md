## sql优化
### 慢日志查询
show variables like 'slow_query_log';  
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/1.jpg' width="50%">  
set global slow_query_log=ON;  
show variables like 'log_queries_not_using_indexes';  
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/2.jpg' width="50%">   
set global log_queries_not_using_indexes=ON;  
show variables like 'long_query_time';  
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/3.jpg' width="50%">  
set global long_query_time=0;  
show variables like 'slow%';  
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/4.jpg' width="50%">  
show variables like 'log%';  
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/5.jpg' width="50%">  
show variables like 'long_query_time';  
use mysql_learn;  
select * from employee_info limit 10;   
tail -n 50 /usr/local/mysql/data/bytedancedeMBP-slow.log;  
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/6.jpg' width="50%">    
mysqldumpslow -n 10 /usr/local/mysql/data/bytedancedeMBP-slow.log | more  
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/7.jpg' width="50%">  

### sql分析
- explain  
explain select * from employee_info;  
&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/8.jpg'>  
    tips:  
    + table:查询关联的表  
    + type:const>eq_ref>ref>range>index>all  
        * const:const说明查询最多有一个匹配行,在主键索引或者唯一索引时使用  
        ```
        explain select * from employee_info where id = 1
        ```
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/9.jpg'>  
        * eq_ref:连接查询中,连接条件使用了主键或者唯一索引  
        ```
        explain select e.company, o.overtime_duration from employee_info e inner join overtime_subsidy o on e.id = o.applicant_id  
        ```  
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/10.jpg'>  
        * ref:查找条件中使用了索引,而且索引不为主键或者unique,使用索引查询第一条数据后还会进行小范围附近扫描  
        ```
        explain select * from employee_info where leader_id = 6787559
        ```        
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/13.jpg'>  
        * range:检索给定范围的行,key说明用了哪个索引,ref列为null  
        ```
        explain select * from product_investment where employee_id < 87
        ```
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/11.jpg'>     
        * index:会扫描索引树,在对结果有排序需求的时候效率高于all  
        ```
        explain select * from employee_info order by name  #using filesort
        explain select * from employee_info order by id    #扫描索引树
        explain select id from employee_info               #索引覆盖,索引包含了需要的信息
        ```
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/14.jpg'>   
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/15.jpg'>  
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/16.jpg'>    
        * all:全表扫描,没有用到索引    
        ```
        explain select * from employee_info where employee_type = '全职'
        ```
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/12.jpg'>   
    + possible keys:可以使用的索引  
    + key:实际使用的索引  
    + key_len:索引的长度  
    + ref:
    + rows:查询必须检查的行数  
    + extra:其他操作信息,例如using filesort, using temporary
        - using filesort:额外操作,低效
        ```
        explain select * from employee_info order by name
        ```
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/14.jpg'>   
        - using temporary:创建了临时表低效
        ```
        explain select distinct month from product_investment where employee_id in (87,1083) 
        ```
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/17.jpg'>   
- 优化准则   
1.查询次数多,查询占用时长多(mysqldumpslow前几条)  
2.IO大的sql(log:关注Rows-examine项,explain:关注rows)  
3.未命中索引的sql(log:关注Rows-examine和Rows Send对比,explain:返回的条数与rows对比) 


### 查询优化
- max
```
explain select max(overtime_day) from overtime_subsidy
```
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/18.jpg'>  

```
create index idx_overtime_day on overtime_subsidy(overtime_day)  
explain select max(overtime_day) from overtime_subsidy
```
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/19.jpg'>  

- count  
    - count(*):返回结果集的行数
    ```
    explain select count(*) from employee_info 
    ```

    - count(column_name):返回该列非null的行数
    ```
    explain select count(overtime_duration) from overtime_subsidy
    ```

- 1
    - 1
        - 1
        - 2
    - 2
- 2