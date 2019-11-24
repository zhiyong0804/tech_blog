# Redis延迟高
#### strace
* strace -f -T -tt -p pid
* -f 表示跟踪子进程和子线程，-T 表示显示系统调用的时长，-tt 表示显示跟踪时间
* PID=$(docker inspect --format {{.State.Pid}} app)

#### nsenter
* nsenter --target $PID --net -- lsof -i
* 进程iowait高，磁盘iowait不高，说明是单个进程使用了一些blocking的磁盘打开方式，比如每次都fsync
* strace -f -e指定系统调用
* 