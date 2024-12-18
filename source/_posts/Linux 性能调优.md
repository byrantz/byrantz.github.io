---
layout:     post
title:      "Linux 性能调优"
date:       2023-10-5 12:00:00
author:     "西洲"
header-style: text
tags:
    - Linux
---

# Linux 性能调优

## CPU

`CPU 使用率` ：就是除了空闲时间外的其他时间占总 CPU 时间的百分比，包括：用户 CPU，系统 CPU

,iowait，硬件中断，软件中断

![image-20240406193421560](http://xizhouyun.top/img1/image-20240406193421560.png)





`平均负载` ：是指单位时间（一分钟）内，系统处于可运行状态和不可中断等待状态的平均进程数，也就是平均活跃进程数（它不仅包含了正在使用 CPU 的进程，还包括等待 CPU 和 等待 IO 的进程），它和 CPU 的使用率没有直接关系。
![image-20240406185247243](http://xizhouyun.top/img1/image-20240406185247243.png)



平均负载越高，证明新进程越难用到 CPU

当平均负载高于 CPU 数量的百分之七十的时候，就应该分析排查负载高的问题。一旦负载过高，就可能导致进程响应变慢，进而影响服务的正常功能。

平均负载高有可能是 CPU 密集型进程导致的； 平均负载高并不一定代表 CPU 使用率高，还有可能是 I/O 更繁忙了； 当发现负载高的时候，你可以使用 mpstat、pidstat 等工具，辅助分析负载的来源



**上下文切换**

过多的上下文切换，会把 CPU 时间消耗在寄存器、内核栈以及虚拟内存等数据的保护和恢复上，缩短进程真正运行的时间，成了系统性能大幅下降的一个元凶



`pistat -w 5` 查看每个进程上下文切换的情况。

![image-20240406192152322](http://xizhouyun.top/img1/image-20240406192152322.png)

`cs(context switch)` 是每秒上下文切换的次数

`nvcswch` 是每秒非自愿上下文切换的次数

`in(interrupt)` 则是每秒中断的次数

所谓自愿上下文切换，是指进程无法获取所需资源，导致的上下文切换。比如说， I/O、 内存等系统资源不足时，就会发生自愿上下文切换。

 而非自愿上下文切换，则是指进程由于时间片已到等原因，被系统强制调度，进而发生的 上下文切换。比如说，大量进程都在争抢 CPU 时，就容易发生非自愿上下文切换



top 统计实时的

ps 统计整个生命周期