## sql优化
### 慢日志查询
show variables like 'slow_query_log';  
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/1.jpg' width=300>  
set global slow_query_log=ON;  
show variables like 'log_queries_not_using_indexes';  
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/2.jpg' width=300>   
set global log_queries_not_using_indexes=ON;  
show variables like 'long_query_time';  <img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/3.jpg' width=300>  
set global long_query_time=0;  
show variables like 'slow%';  
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/4.jpg' width=300>  
show variables like 'log%';  
<img src='https://github.com/unbelievableme/intership-learning/blob/master/image/mysql/5.jpg' width=300>  
show variables like 'long_query_time';
### 查询优化