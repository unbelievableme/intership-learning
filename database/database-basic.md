## 数据库设计理论
- 范式  
    - 第一范式  
    - 第二范式  
    - 第三范式  
- er图  

## 事务  
- 概念
- ACID  
    - 原子性  
    - 一致性  
    - 隔离性   
    - 持久性  

## 一致性问题  
- 更新丢失  

- 脏读  

- 不可重复读    

- 幻读  


## 隔离级别  
- READ UNCOMMITTED   

- READ COMMITTED   

- REPEATABLE READ  
 
- SERIALIZABLE  

## MVCC
- 版本号  

- 实现READ COMMITTED  

- 实现REPEATABLE READ  

## 行锁算法
- Record Lock  

- Gap Lock  

- Next-Key Lock  

- 应用  
    ```
    # 在REPEATABLE READ下
    select * from table_name in share mode; #(share Next-Key Lock)  
    select * from table_name for update; #(share Next-Key Lock)  

    # 在SERIALIZABLE下  
    select * from table_name  
    ```

## 悲观/乐观锁  
- 实现  

- 实例  
    - 悲观   
        - 数据库中的行锁,表锁  
        - java中的synchronized加锁机制
    - 乐观  
        - java中的CAS机制  