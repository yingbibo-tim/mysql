### note for mysql

#### mysql组合索引

> 1. 最左原则
> 2. 覆盖索引。如果查询的结果是组合索引里面的字段或者是主键,则不需要回表操作
> 3. 联合查询 是根据索引的创建顺序,以最左原则进行where检索，比如（age,name）以age=1 或者 age=1 and name = ‘张三’ 是可以使用索引的,但是如果单单以 name = ‘张三’ 则是不会使用索引
> 4.索引下推 select * like 'hello%' and age > 10  5.6之前没这个功能,会进行回表操作，5.6之后 会先过滤age<10的数据 然后进行回表操作，查出其他的字段


#### mysql锁

> 1.全局锁 Flush tables with read lock (FTWRL),数据库处于只读，使用场景主要是数据库全库逻辑备份

> 2.表级锁 lock tables t1 read,lock tables t2 write,其他现在只能对t1读 不能写,对t2读写都不允许
5.5以后 增加了 metadata lock


> 3.行锁


#### 配置参数

> 1. innodb_io_capacity : 刷新脏页能力 **脏页：内存数据跟硬盘数据不一致**
> 2. innodb_buffer_pool_size : 缓冲池大小
> 3. innodb_buffer_pool_instances : 缓冲池数量
> 4. innodb_max_dirty_pages_pct : 脏页比例上限 默认 75%  
> 5. innodb_flush_neighbors : 找邻居 刷脏页的时候 顺带把旁边的脏页也刷了（值为1的时候）在机械硬盘的时候 这个值最好设置为1，因为随机IO读写的代价是很高的，在ssd上可以设置为0，自己是刷自己的 mysql8.0 默认设置为0了


