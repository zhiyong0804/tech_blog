# mysql复制
#### load data
* 不加“local”，是读取服务端的文件，这个文件必须在 secure_file_priv 指定的目录或子目录下；
* 加上“local”，读取的是客户端的文件，只要 mysql 客户端有访问这个文件的权限即可。这时候，MySQL 客户端会先把本地文件传给服务端，然后执行上述的 load data 流程。

#### 物理复制表结构
* 执行 create table r like t，创建一个相同表结构的空表；
* 执行 alter table r discard tablespace，这时候 r.ibd 文件会被删除；
* 执行 flush table t for export，这时候 db1 目录下会生成一个 t.cfg 文件；
* 在 db1 目录下执行 cp t.cfg r.cfg; cp t.ibd r.ibd；这两个命令；
* 执行 unlock tables，这时候 t.cfg 文件会被删除；
* 执行 alter table r import tablespace，将这个 r.ibd 文件作为表 r 的新的表空间，由于这个文件的数据内容和 t.ibd 是相同的，所以表 r 中就有了和表 t 相同的数据。

#### 对比
* 物理拷贝必须全表拷贝
* 需要到服务器上拷贝
* 源表和目标表都是InnoDB引擎才能使用
* mysqldump 不能使用join比较复杂的where条件写法
* select 每次智能导出一张表的数据，表结构也需要另外的语句单独备份
* 逻辑备份可以跨引擎使用

#### 