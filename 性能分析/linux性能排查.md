0. 系统整体
    1. vmstat 1：每秒输出一次系统核心指标
    2. dmesg | tail：输出系统日志最后10行
1. CPU
    1. uptime：快速查看系统CPU负载情况：1分钟、5分钟、15分钟
    2. top：整体负载情况，通过P来排序CPU，通过M来排序内存
    3. mpstat -P ALL 1：每秒输出每个 CPU 的占用情况，如果有一个 CPU 占用率特别高，那么有可能是一个单线程应用程序引起的
2. 内存
    1. free -m：内存使用情况
3. 磁盘IO
    1. iostat -xz 1：每秒输出机器磁盘 IO 情况
4. 网络IO
    1. sar -n DEV 1: 每秒输出网络设备的吞吐率
    2. sar -n TCP,ETCP 1：每秒输出TCP连接状态
    3. route -n：路由信息
    4. dig,nslookup：DNS状态
    5. traceroute：路由节点状况与延时
5. 进程
    1. pidstat 1：每秒输出进程的CPU占用率
    2. tcpdump: 网络包协议追踪
    3. netstat：tcp连接信息
    4. ps -ef / -aux
    5. strace -p pid