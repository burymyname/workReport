### 测试报告

#### 测试环境

使用两台物理机H110
```
客户端 Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz
服务端 Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz
```

这里没有连接万兆网卡
<br/>
<br/>

以下参数两台机器均相同

查看网卡设置
```
# ethtool enp2s0
Settings for enp2s0:
        Supported ports: [ TP ]
        Supported link modes:   10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
        Supported pause frame use: Symmetric
        Supports auto-negotiation: Yes
        Advertised link modes:  10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
        Advertised pause frame use: Symmetric
        Advertised auto-negotiation: Yes
        Speed: 1000Mb/s
        Duplex: Full
        Port: Twisted Pair
        PHYAD: 1
        Transceiver: internal
        Auto-negotiation: on
        MDI-X: off (auto)
        Supports Wake-on: pumbg
        Wake-on: g
        Current message level: 0x00000007 (7)
                               drv probe link
        Link detected: yes

```

物理CPU个数以及单个物理CPU核心数
```
# cat /proc/cpuinfo |grep "physical id"|sort|uniq|wc -l
1
# cat /proc/cpuinfo |grep "processor"|wc -l
4
```

内核版本号
```
# uname -a
Linux 56 4.15.18 #3 SMP Wed May 13 20:19:18 CST 2020 x86_64 x86_64 x86_64 GNU/Linux
```

发行版本
```
# cat /etc/issue
Ubuntu 16.04.6 LTS \n \l
```


网络性能参数
```
# cat /proc/sys/net/core/wmem_default 默认发送窗口大小
8388608 = 8M

# cat /proc/sys/net/core/wmem_max 最大发送窗口大小
16777216 = 16M

# cat /proc/sys/net/core/rmem_default 默认接收窗口大小
8388608 = 8M

# cat /proc/sys/net/core/rmem_max 最大接收窗口大小
16777216 = 16M

#  cat /proc/sys/net/ipv4/tcp_rmem
4096    131072  6291456
4K      128K    6M

# cat /proc/sys/net/ipv4/tcp_wmem
4096    16384   4194304
4K      16K     
```

### 测试目标

1.测试1:`bench_io_select_udp_tun`
* 服务端 (IP:10.0.80.56 TUN:10.2.0.2)
```
iperf3 -s -i 2 
./bench_io_select_udp_tun --tun-address 10.2.0.2 --address 10.0.80.54 
pidstat 1
```

* 客户端 (IP:10.0.80.54 TUN:10.2.0.1)
```
./bench_io_select_udp_tun --tun-address 10.2.0.1 --address 10.0.80.56 
iperf3 -c 10.2.0.2 -i 2 -t 30
```

实验结果

* 服务端
```
root@56:/# pidstat 2
Average:      UID       PID    %usr %system  %guest    %CPU   CPU  Command
Average:        0      4769    4.50   12.10    0.00   16.60     -  iperf3
Average:        0      4770    6.90   18.00    0.00   24.90     -  bench_io_select

Accepted connection from 10.2.0.3, port 25652
[  5] local 10.2.0.1 port 5201 connected to 10.2.0.3 port 25654
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-2.00   sec   199 MBytes   837 Mbits/sec
[  5]   2.00-4.00   sec   222 MBytes   931 Mbits/sec
[  5]   4.00-6.00   sec   222 MBytes   931 Mbits/sec
[  5]   6.00-8.00   sec   222 MBytes   931 Mbits/sec
[  5]   8.00-10.00  sec   222 MBytes   931 Mbits/sec
[  5]  10.00-10.05  sec  5.97 MBytes   931 Mbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-10.05  sec  1.07 GBytes   912 Mbits/sec                  receiver

```
* 客户端
```
root@54:~/openssl-1.1.1d# iperf3 -c 10.2.0.2 -i 2 -t 30
Connecting to host 10.2.0.2, port 5201
[  5] local 10.2.0.1 port 44816 connected to 10.2.0.2 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-2.00   sec   207 MBytes   869 Mbits/sec   69   1.33 MBytes
[  5]   2.00-4.00   sec   220 MBytes   923 Mbits/sec    1   1.05 MBytes
[  5]   4.00-6.00   sec   201 MBytes   844 Mbits/sec    0   1.18 MBytes
[  5]   6.00-8.00   sec   220 MBytes   923 Mbits/sec    0   1.30 MBytes
[  5]   8.00-10.00  sec   222 MBytes   933 Mbits/sec    0   1.42 MBytes
[  5]  10.00-12.00  sec   211 MBytes   886 Mbits/sec    1   1.15 MBytes
[  5]  12.00-14.00  sec   220 MBytes   923 Mbits/sec    0   1.34 MBytes
[  5]  14.00-16.00  sec   222 MBytes   933 Mbits/sec    0   1.44 MBytes
[  5]  16.00-18.00  sec   221 MBytes   928 Mbits/sec    0   1.48 MBytes
[  5]  18.00-20.00  sec   210 MBytes   881 Mbits/sec    4   1.20 MBytes
[  5]  20.00-22.00  sec   221 MBytes   928 Mbits/sec    0   1.37 MBytes
[  5]  22.00-24.00  sec   222 MBytes   933 Mbits/sec    0   1.45 MBytes
[  5]  24.00-26.00  sec   222 MBytes   933 Mbits/sec    0   1.48 MBytes
[  5]  26.00-28.00  sec   201 MBytes   844 Mbits/sec    3   1.17 MBytes
[  5]  28.00-30.00  sec   219 MBytes   917 Mbits/sec    0   1.29 MBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-30.00  sec  3.17 GBytes   907 Mbits/sec   78             sender
[  5]   0.00-30.04  sec  3.16 GBytes   905 Mbits/sec                  receiver

iperf Done.

```


2.测试2:`bench_io_boostasio_udp_tun`
* 服务端  (IP:10.0.80.56 TUN:10.2.0.2)
```
iperf3 -s -i 2 
 ./bench_io_boostasio_udp_tun --tun-address 10.2.0.2 --address 10.0.80.54 
pidstat 1
```

* 客户端  (IP:10.0.80.54 TUN:10.2.0.1)
```
./bench_io_boostasio_udp_tun --tun-address 10.2.0.1 --address 10.0.80.56 
iperf3 -c 10.2.0.2 -i 2 -t 30

```

实验结果
* 服务端
```
Average:      UID       PID    %usr %system  %guest    %CPU   CPU  Command
Average:        0      4769    6.35   17.34    0.00   23.70     -  iperf3
Average:        0      4770    9.06   26.05    0.00   35.12     -  bench_io_select

```
* 客户端
```
root@54:~# iperf3 -c 10.2.0.2 -i 2 -t 30
Connecting to host 10.2.0.2, port 5201
[  5] local 10.2.0.1 port 44834 connected to 10.2.0.2 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-2.00   sec   201 MBytes   844 Mbits/sec   61   1.32 MBytes
[  5]   2.00-4.00   sec   221 MBytes   928 Mbits/sec    0   1.48 MBytes
[  5]   4.00-6.00   sec   204 MBytes   855 Mbits/sec    1   1.17 MBytes
[  5]   6.00-8.00   sec   219 MBytes   917 Mbits/sec    0   1.29 MBytes
[  5]   8.00-10.00  sec   221 MBytes   928 Mbits/sec    0   1.41 MBytes
[  5]  10.00-12.00  sec   215 MBytes   902 Mbits/sec    1   1.11 MBytes
[  5]  12.00-14.00  sec   218 MBytes   912 Mbits/sec    0   1.31 MBytes
[  5]  14.00-16.00  sec   221 MBytes   928 Mbits/sec    0   1.43 MBytes
[  5]  16.00-18.00  sec   222 MBytes   933 Mbits/sec    0   1.48 MBytes
[  5]  18.00-20.00  sec   206 MBytes   865 Mbits/sec    2   1.14 MBytes
[  5]  20.00-22.00  sec   214 MBytes   897 Mbits/sec    0   1.25 MBytes
[  5]  22.00-24.00  sec   222 MBytes   933 Mbits/sec    0   1.37 MBytes
[  5]  24.00-26.00  sec   221 MBytes   928 Mbits/sec    1   1.04 MBytes
[  5]  26.00-28.00  sec   210 MBytes   881 Mbits/sec    0   1.27 MBytes
[  5]  28.00-30.00  sec   221 MBytes   928 Mbits/sec    0   1.40 MBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-30.00  sec  3.16 GBytes   905 Mbits/sec   66             sender
[  5]   0.00-30.04  sec  3.16 GBytes   904 Mbits/sec                  receiver

iperf Done.
```

3.测试3:`/bench_io_tun`

单机测试


1. 设置tun地址
```
ifconfig tun0 10.10.10.10/24 up
```

2. terminal窗口1
```
./bench_io_tun &
```

3. terminal窗口2
```
iperf3 -s -i 2
```

4. terminal窗口1
```
iperf3 -c 10.10.10.1 -i 2
```

测试结果
* 窗口1(服务端)
```
root@54:/# iperf3 -s -i 2
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
Accepted connection from 10.10.10.1, port 37440
[  5] local 10.10.10.10 port 5201 connected to 10.10.10.1 port 37442
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-2.00   sec   634 MBytes  2.66 Gbits/sec
[  5]   2.00-4.00   sec   647 MBytes  2.71 Gbits/sec
[  5]   4.00-6.00   sec   647 MBytes  2.71 Gbits/sec
[  5]   6.00-8.00   sec   644 MBytes  2.70 Gbits/sec
[  5]   8.00-10.00  sec   640 MBytes  2.69 Gbits/sec
[  5]  10.00-10.04  sec  12.4 MBytes  2.69 Gbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-10.04  sec  3.15 GBytes  2.70 Gbits/sec                  receiver

```

* 窗口2(客户端)
```
root@54:~/openssl-1.1.1d# iperf3 -c 10.10.10.1 -i 2
Connecting to host 10.10.10.1, port 5201
[  5] local 10.10.10.10 port 37442 connected to 10.10.10.1 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-2.00   sec   647 MBytes  2.72 Gbits/sec    0    385 KBytes
[  5]   2.00-4.00   sec   648 MBytes  2.72 Gbits/sec    0    385 KBytes
[  5]   4.00-6.00   sec   648 MBytes  2.72 Gbits/sec    0    385 KBytes
[  5]   6.00-8.00   sec   644 MBytes  2.70 Gbits/sec    0    385 KBytes
[  5]   8.00-10.00  sec   640 MBytes  2.69 Gbits/sec    0    385 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  3.15 GBytes  2.71 Gbits/sec    0             sender
[  5]   0.00-10.04  sec  3.15 GBytes  2.70 Gbits/sec                  receiver

iperf Done.

```