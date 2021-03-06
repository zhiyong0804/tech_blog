# Linux IO操作
#### 虚拟文件系统
* 目录项，记录了文件的名字，以及文件与其他目录项之间的目录关系。
* 索引节点，记录了文件的元数据。
* 逻辑块，是由连续磁盘扇区构成的最小读写单元，用来存储文件数据。
* 超级块，用来记录文件系统整体的状态，如索引节点和逻辑块的使用情况等。

#### 固态硬盘和机械磁盘
* 机械磁盘的最小读写单位是扇区，一般大小为 512 字节。
* 而固态磁盘的最小读写单位是页，通常大小是 4KB、8KB 等。

#### 通用块层
* 为文件系统和应用程序，提供访问块设备的标准接口；向下，把各种异构的磁盘设备抽象为统一的块设备，并提供统一框架来管理这些设备的驱动程序。
* 通用块层还会给文件系统和应用程序发来的 I/O 请求排队，并通过重新排序、请求合并等方式，提高磁盘读写的效率。

#### 磁盘性能指标
* 使用率，是指磁盘处理 I/O 的时间百分比。过高的使用率（比如超过 80%），通常意味着磁盘 I/O 存在性能瓶颈。
* 饱和度，是指磁盘处理 I/O 的繁忙程度。过高的饱和度，意味着磁盘存在严重的性能瓶颈。当饱和度为 100% 时，磁盘无法接受新的 I/O 请求。
* IOPS（Input/Output Per Second），是指每秒的 I/O 请求数。
* 吞吐量，是指每秒的 I/O 请求大小。
* 响应时间，是指 I/O 请求从发出到收到响应的间隔时间。
* 在数据库、大量小文件等这类随机读写比较多的场景中，IOPS 更能反映系统的整体性能；而在多媒体等顺序读写较多的场景中，吞吐量才更能反映系统的整体性能。

#### iostat指标
* %util 磁盘IO使用率
* r/s + w/s 就是IOPS
* rkB/s+wkB/s 吞吐量
* r_await+w_await 响应时间
* 