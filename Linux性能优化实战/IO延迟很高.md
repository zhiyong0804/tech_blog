# IO延迟很高

#### 追踪流程
* top
* iowait
* iostat -d -x 1
* pidstat -d 1
* strace -fp pid

#### filetop
* 主要跟踪内核中文件的读写情况，并输出线程 ID（TID）、读写大小、读写类型以及文件名称。
* eBPF内核追踪的原理

#### opensnoop
* 动态追踪内核中的open系统调用

#### 追踪流程
* top
* iostat
* pidstat
* strace
* lsof
* filetop
* opensnoop
* 通过文件找到正在操作的数据库和表
* 
