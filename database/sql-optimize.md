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
    + select_type:select语句的类型  
        * simple: 简单查询,不包含子查询和连接查询  
        * subquery: 子查询中的第一个select,并且不依赖外部表  
        * dependent_subquery: 与subquery区别是依赖外部查询  
        * materialized: 物化查询  
        ```
        explain select * from product_investment where month in (select overtime_day from overtime_subsidy)
        ```  
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/24.jpg'>  
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
        - using index  
        覆盖索引:查询的数据通过扫描索引就可以获得  
        - using where   
        需要回表来筛选数据,例如：
        ```
        select * from employee_info where leader_id = 1 and name = 'xxx' # leader_id上有索引,name没有
        ```
        - using index condition  
        先过滤索引然后where过滤数据,例如:  
        ```
        explain select * from employee_info where leader_id > 1
        ```
        

- 优化准则   
    - 查询次数多,查询占用时长多(mysqldumpslow前几条)  
    - IO大的sql(log:关注Rows-examine项,explain:关注rows)  
    - 未命中索引的sql(log:关注Rows-examine和Rows Send对比,explain:返回的条数与rows对比) 


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
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/20.jpg'>   
    - count(column_name):返回该列非null的行数
    ```
    explain select count(employee_type) from employee_info
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/21.jpg'>   

    ```
    explain select count(en_name) from employee_info #与上面对比
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/30.jpg'>    

- 子查询
    - in + 子查询  
    ```
    explain select * from product_investment where employee_id in (select id from employee_info where leader_id = 1)   
    show warnings;
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/22.jpg'>   
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/23.jpg'>

    ```
    explain select * from product_investment where employee_id = (select id from employee_info where leader_id = 1 limit 1)  
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/26.jpg'>  

    ```
    explain select * from product_investment where exists(select 1 from employee_info where leader_id = 1 and employee_info.id = product_investment.employee_id)  
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/27.jpg'>  

    ```
    explain select * from product_investment where month in (select overtime_day from overtime_subsidy);  
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/28.jpg'>  

    - from + 子查询  
    ```
    explain select p.create_time from product_investment p ,(select id from employee_info where leader_id = 1) s where s.id = p.employee_id;  
    show warnings;  
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/25.jpg'>    
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/29.jpg'>    

- join
    - BNLJ(Block Nested-Loop Join)
    ```
    explain select * from overtime_subsidy o inner join role_employee r where o.applicant_id = r.employee_id and r.role_id = 106
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/31.jpg'>   

    - INLJ(Index Nested-Loop Join)
    ```
    create index idx_employee_id on role_employee(employee_id)   
    explain select * from overtime_subsidy o inner join role_employee r where o.applicant_id = r.employee_id and r.role_id = 106  
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/32.jpg'>  

    上述算法详解可以参考<a href ="https://blog.csdn.net/orangleliu/article/details/72850659">这里</a>

- 索引
    - 建立索引
        ```
        show index from employee_info
        ```
        &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/33.jpg'>   
        - tips:
            + Cardinality :散列程度, Cardinality/总记录数越大,索引的价值越高  
            + Index_type: 索引的数据结构类型  
    - 组合索引  
        ```
        create index idx_A_B on table_name(A,B)
        ```  
        - 用到组合索引:  
            - A > 1  
            - A = 1 and B =1  
            - A = 1 and B >1  
            - A in (1,2) and B > 1  
            - A in (1,2) and B = 1  
            - A = 1 and B in (1,2)  
        - 用到部分索引:  
            - A > 1 and B = 1 (A走索引,B扫表))  
        - 没用索引:  
            - B = 1  
            - B > 1  
            - A in (1,2) and B in (1,2)
    - 合并索引  
        名词解释:对多个索引分别进行条件扫描，然后将它们各自的结果进行合并(intersect/union),详情参考<a href="https://www.cnblogs.com/digdeep/p/4975977.html">这里</a>
        ```
        create index idx_A on table_name(A)
        create index idx_B on table_name(B)
        ```
        exp1:
        ```
        select * from table_name where A = 1 and B = 1
        ```
        explain的type为index_merge,extra为using intersect(A,B),针对这种情况可以建立A,B的联合索引,这样就只需要扫描一次索引而不是两次

        exp2:
        ```
        select * from table_name where A = 1 or B = 1
        ```
        explain的type为idnex_merge，extra为using union(A,B),在这种情况下mysql自带的index_merge
        
- order-by
    - 扫描索引排序
        ```
        create index idx_A_B on table_name(A,B)
        ```
        - order-by走索引:  
            - order by A
            - A = 1 order by B
            - order by A,B #两列必须相同顺序排序
        - order-by不走索引:  
            - order by B
            - A > 1 order by B
            - A in (1,2) order by B
            - order by A , B desc

    - 文件排序
        - 双路排序
        - 单路排序


- group by

- distinct

- limit   
    常见sql:  
    ```
    select * from table_name limit a , b # 当a特别大的时候效率很低
    ```

    效率低的原因:  
    >数据库的数据存储并不是像我们想象中那样，按表按顺序存储数据，一方面是因为计算机存储本身就是随机读写，另一方面是因为数据的操作有很大的随机性，即使一开始数据的存储是有序的，经过一系列的增删查改之后也会变得凌乱不堪。所以数据库的数据存储是随机的，使用 B+Tree， Hash 等方式组织索引。所以当你让数据库读取第 10001 条数据的时候，数据库就只能一条一条的去查去数  

    优化:
    ```
    select * from table_name where id > (select id from table_name limit a , 1) limit b
    ```

    ```
    select * from table_name inner join (select id from table_name limit a ,b ) t using(id)
    ```
    总的思路就是查询的id通过二级索引可以获得,避免了扫表,提高了效率,具体参考<a href="https://www.jianshu.com/p/77eaad62f974">索引覆盖</a>  

    下述实际例子:  
    ```
    explain select * from employee_info where id > (select id from employee_info limit 1000 , 1) limit 10  
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/38.jpg'>   

    ```
    explain select * from employee_info inner join (select id from employee_info limit 1000,10) a using(id)
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/34.jpg'>   

- like  
    - like 'keyword%'
    ```
    create index idx_name on employee_info(name)  
    explain select * from employee_info where name like '张%'
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/35.jpg'>   

    - like '%keyword'

    ```
    explain select * from employee_info where name like '%张%'  
    drop index idx_name on employee_info(name)  
    create FULLTEXT index idx_name on employee_info(name)  
    explain select * from employee_info where name like '%张%'   
    ```
    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/37.jpg'> 

    &nbsp;&nbsp;<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/36.jpg'>  

    注意：mysql支持的全文索引,按照分词匹配,例如name=('张三', '李四')) or ('张三' '李四'))

## 参考文献
http://www.cnblogs.com/zhengyun_ustc/p/slowquery1.html  
http://www.cnblogs.com/micrari/p/6583482.html   
https://www.cnblogs.com/micrari/p/6921806.html  
https://blog.csdn.net/orangleliu/article/details/72850659    
https://www.jianshu.com/p/efecd0b66c55  

