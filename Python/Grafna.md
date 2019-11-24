# Grafna
#### 监控案例
* CPU使用率
* 除去IO等待造成的CPU高之外，大部 分情况 就是 user%造成
另外， 
* system% 内核态的CPU 使用率 偶尔也会出现高的情况
* 当服务器硬盘IO占用过大时，CPU会等待IO的返回进入interuptable 类型的CPU等待时间

#### 硬盘监控
* predict_linear()
* 磁盘读写((rate(node_disk_read_bytes_total[1m] )+ rate(node_disk_written_bytes_total[1m])) / 1024 /1024) > 0