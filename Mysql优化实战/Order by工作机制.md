# Order by工作机制
#### 插入数据函数
```
* delimiter ;;
create procedure icity()
begin
  declare i int;
  set i=1;
  while(i<=100000)do
    if i <= 4000 then
      insert into t values(i, "杭州", substring(MD5(RAND()),1,10), ROUND(RAND()*(100-1)+1), "杭州中路");
    else
      insert into t values(i, "深圳", substring(MD5(RAND()),1,10), ROUND(RAND()*(100-1)+1), "深南大道");
    end if;
    set i=i+1;
  end while;
end;;
delimiter ;
```

#### 全字段排序
* select city,name,age from t where city='杭州' order by name limit 1000;
* 初始化 sort_buffer，确定放入 name、city、age 这三个字段；

* 从索引 city 找到第一个满足 city='杭州’条件的主键 id，也就是图中的 ID_X；

* 到主键 id 索引取出整行，取 name、city、age 三个字段的值，存入 sort_buffer 中；

* 从索引 city 取下一个记录的主键 id；

* 重复步骤 3、4 直到 city 的值不满足查询条件为止，对应的主键 id 也就是图中的 ID_Y；

* 对 sort_buffer 中的数据按照字段 name 做快速排序；

* 按照排序结果取前 1000 行返回给客户端

#### row_id排序
* 相对全字段排序遍历排序结果，取前 1000 行，并按照 id 的值回到原表中取出 city、name 和 age 三个字段返回给客户端。

#### 覆盖索引
* 覆盖索引是指，索引上的信息足够满足查询请求，不需要再回到主键索引上去取数据。
* 