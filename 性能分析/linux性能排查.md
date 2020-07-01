1. uptime：快速查看系统CPU负载情况：1分钟、5分钟、15分钟
2. dmesg | tail：输出系统日志最后10行
3. vmstat 1：每秒输出一次系统核心指标
4. mpstat -P ALL 1：每秒输出每个 CPU 的占用情况，如果有一个 CPU 占用率特别高，那么有可能是一个单线程应用程序引起的
5. pidstat 1：每秒输出进程的CPU占用率
6. iostat -xz 1：每秒输出机器磁盘 IO 情况
7. free -m：内存使用情况
8. sar -n DEV 1: 每秒输出网络设备的吞吐率
9. sar -n TCP,ETCP 1：每秒输出TCP连接状态
10. top：整体负载情况，通过P来排序CPU，通过M来排序内存
11. route -n：路由信息
12. dig,nslookup：DNS状态
13. traceroute：路由节点状况与延时
14. tcpdump: 网络包协议追踪
15. netstat：tcp连接信息