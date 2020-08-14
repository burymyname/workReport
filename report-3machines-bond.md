# 双网卡bond三台物理机实验数据

## 测试环境

### 物理机环境

```
[54]  Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz
[56]  Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz
[163] Intel(R) Core(TM) i5-2400 CPU @ 3.10GHz
```

### 网卡配置

- bond0：54与163

- bond1：54与56

- bond0与bond1配置相同

* bond mode = 0

```
#ethtool bond0
Settings for bond0:
        Supported ports: [ ]
        Supported link modes:   Not reported
        Supported pause frame use: No
        Supports auto-negotiation: No
        Advertised link modes:  Not reported
        Advertised pause frame use: No
        Advertised auto-negotiation: No
        Speed: 2000Mb/s
        Duplex: Full
        Port: Other
        PHYAD: 0
        Transceiver: internal
        Auto-negotiation: off
        Link detected: yes
```



### bond直连数据

##### 54与56，两台H110，测速基本到达峰值

```
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
Accepted connection from 192.168.200.3, port 42846
[  5] local 192.168.200.4 port 5201 connected to 192.168.200.3 port 42848
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec   179 MBytes  1.50 Gbits/sec
[  5]   1.00-2.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   2.00-3.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   3.00-4.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   4.00-5.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   5.00-6.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   6.00-7.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   7.00-8.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   8.00-9.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   9.00-10.00  sec   226 MBytes  1.90 Gbits/sec
[  5]  10.00-10.04  sec  8.95 MBytes  1.90 Gbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-10.04  sec  2.17 GBytes  1.86 Gbits/sec                  receiver
-----------------------------------------------------------


Connecting to host 192.168.200.4, port 5201
[  5] local 192.168.200.3 port 42848 connected to 192.168.200.4 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec   191 MBytes  1.60 Gbits/sec  270    502 KBytes
[  5]   1.00-2.00   sec   226 MBytes  1.90 Gbits/sec    0    502 KBytes
[  5]   2.00-3.00   sec   226 MBytes  1.90 Gbits/sec    0    502 KBytes
[  5]   3.00-4.00   sec   227 MBytes  1.90 Gbits/sec    0    502 KBytes
[  5]   4.00-5.00   sec   225 MBytes  1.89 Gbits/sec    0    502 KBytes
[  5]   5.00-6.00   sec   227 MBytes  1.91 Gbits/sec    0    502 KBytes
[  5]   6.00-7.00   sec   226 MBytes  1.90 Gbits/sec    0    502 KBytes
[  5]   7.00-8.00   sec   227 MBytes  1.90 Gbits/sec    0    502 KBytes
[  5]   8.00-9.00   sec   226 MBytes  1.89 Gbits/sec    0    502 KBytes
[  5]   9.00-10.00  sec   227 MBytes  1.90 Gbits/sec    0    502 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  2.18 GBytes  1.87 Gbits/sec  270             sender
[  5]   0.00-10.04  sec  2.17 GBytes  1.86 Gbits/sec                  receiver
```



### bond直连数据

##### 54与163，一台H61，一台H110，测速基本到达峰值

```
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
Accepted connection from 192.168.100.1, port 47828
[  5] local 192.168.100.2 port 5201 connected to 192.168.100.1 port 47830
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec   201 MBytes  1.68 Gbits/sec
[  5]   1.00-2.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   2.00-3.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   3.00-4.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   4.00-5.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   5.00-6.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   6.00-7.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   7.00-8.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   8.00-9.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   9.00-10.00  sec   226 MBytes  1.90 Gbits/sec
[  5]  10.00-10.00  sec   231 KBytes  1.55 Gbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-10.00  sec  2.19 GBytes  1.88 Gbits/sec                  receiver
-----------------------------------------------------------


Connecting to host 192.168.100.2, port 5201
[  4] local 192.168.100.1 port 47830 connected to 192.168.100.2 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec   204 MBytes  1.71 Gbits/sec  514    435 KBytes
[  4]   1.00-2.00   sec   226 MBytes  1.90 Gbits/sec    0    435 KBytes
[  4]   2.00-3.00   sec   226 MBytes  1.90 Gbits/sec    0    436 KBytes
[  4]   3.00-4.00   sec   227 MBytes  1.90 Gbits/sec    0    438 KBytes
[  4]   4.00-5.00   sec   226 MBytes  1.90 Gbits/sec    0    438 KBytes
[  4]   5.00-6.00   sec   226 MBytes  1.90 Gbits/sec   20    441 KBytes
[  4]   6.00-7.00   sec   226 MBytes  1.89 Gbits/sec    0    453 KBytes
[  4]   7.00-8.00   sec   227 MBytes  1.91 Gbits/sec    0    453 KBytes
[  4]   8.00-9.00   sec   226 MBytes  1.90 Gbits/sec    0    453 KBytes
[  4]   9.00-10.00  sec   227 MBytes  1.90 Gbits/sec    0    456 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec  2.19 GBytes  1.88 Gbits/sec  534             sender
[  4]   0.00-10.00  sec  2.19 GBytes  1.88 Gbits/sec                  receiver
```



### bond三台实验数据

##### io_select_udp_tun 拓扑图

- client: 10.0.80.163 <br>
tun: 10.2.0.1 <br>
bond0: 192.168.100.1

- VPN server: 10.0.80.54 <br>
tun: 10.2.0.2 <br>
bond0: 192.168.100.2 <br>
bond1: 192.168.200.3

- server: 10.0.80.56 <br>
bond1: 192.168.200.4

```
+----------------------------------------------------------------------------------------------------------------------+
|      10.0.80.56                                       10.0.80.54                                      10.0.80.163    |
|        server                                         VPN server                                        client       |
+----------------------------------------------------------------------------------------------------------------------+
               
  +-------------------+                                     SNAT                               +-----------------------+
  |                   |                              iptables -t nat  \                        |                       |
  |     iperf3 -s     |                                -A POSTROUTING \                        |iperf3 -c 192.168.200.4|
  |                   |                                -s 10.2.0.1    \                        |                       |
  +-------------------+                                -o [bond1]     \                        +-----------------------+
            ↑                                          -j MASQUERADE                                      |
            |                                                 |                                           |
            |                                   +-------------+                                           ↓
            |                                   ↓      +-------------+                             +-------------+ 
            |                             +------------|tun[10.2.0.2]|                             |tun[10.2.0.1]|
            |                             |            +-------------+                             +-------------+
            |                             |                 ↑src:10.2.0.1                             |src:10.2.0.1
            |                             |                 |                                         |
            |                             |                 |dst:192.168.200.4                        ↓dst:192.168.200.4
            |                             |   +------------------------------+            +-----------------------------+
            |                             |   |  ./bench_io_select_udp_tun \ |            | ./bench_io_select_udp_tun \ |
            |                             |   |   --tun-address 10.2.0.2   \ |            |   --tun-address 10.2.0.1  \ |
            |                             |   |   --address 192.168.100.1    |            |   --address 192.168.100.2   |
            |                             |   +------------------------------+            +------ ----------------------+
            |                             |                   ↑                                           |
            |                             +-------------+     |                                           |
            |                                           ↓     |                                           ↓
  +-------------------+        bond mode0           +--------------------+       bond mode0     +--------------------+
  |bond[192.168.200.4]|<---------------------+      |bond1[192.168.100.2]|<---------------------|bond1[192.168.100.1]|
  +-------------------+     src:192.168.200.3|      +--------------------+                      +--------------------+
                            dst:192.168.200.4+------|bond0[192.168.200.3]|                  +-----------------+
                                                    +--------------------+ src:192.168.100.1|src:10.2.0.1     |
                                                                           dst:192.168.100.2|dst:192.168.200.4|
                                                                                            +-----------------+



```



```
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
Accepted connection from 192.168.200.3, port 36660
[  5] local 192.168.200.4 port 5201 connected to 192.168.200.3 port 36662
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-2.00   sec   172 MBytes   720 Mbits/sec
[  5]   2.00-4.00   sec   176 MBytes   739 Mbits/sec
[  5]   4.00-6.00   sec   174 MBytes   730 Mbits/sec
[  5]   6.00-8.00   sec   182 MBytes   762 Mbits/sec
[  5]   8.00-10.00  sec   184 MBytes   770 Mbits/sec
[  5]  10.00-12.00  sec   184 MBytes   770 Mbits/sec
[  5]  12.00-14.00  sec   183 MBytes   768 Mbits/sec
[  5]  14.00-16.00  sec   184 MBytes   772 Mbits/sec
[  5]  16.00-18.00  sec   184 MBytes   771 Mbits/sec
[  5]  18.00-20.00  sec   184 MBytes   772 Mbits/sec
[  5]  20.00-22.00  sec   184 MBytes   772 Mbits/sec
[  5]  22.00-24.00  sec   184 MBytes   774 Mbits/sec
[  5]  24.00-26.00  sec   185 MBytes   775 Mbits/sec
[  5]  26.00-28.00  sec   183 MBytes   769 Mbits/sec
[  5]  28.00-30.00  sec   208 MBytes   871 Mbits/sec
[  5]  30.00-30.05  sec  5.48 MBytes   986 Mbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-30.05  sec  2.69 GBytes   769 Mbits/sec                  receiver
-----------------------------------------------------------


Connecting to host 192.168.200.4, port 5201
[  4] local 10.2.0.1 port 36662 connected to 192.168.200.4 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-2.00   sec   178 MBytes   746 Mbits/sec   10    680 KBytes
[  4]   2.00-4.00   sec   176 MBytes   739 Mbits/sec    7    681 KBytes
[  4]   4.00-6.00   sec   174 MBytes   729 Mbits/sec    3    601 KBytes
[  4]   6.00-8.00   sec   182 MBytes   765 Mbits/sec    6    592 KBytes
[  4]   8.00-10.00  sec   184 MBytes   771 Mbits/sec    1    631 KBytes
[  4]  10.00-12.00  sec   184 MBytes   771 Mbits/sec    2    629 KBytes
[  4]  12.00-14.00  sec   182 MBytes   765 Mbits/sec    6    533 KBytes
[  4]  14.00-16.00  sec   184 MBytes   771 Mbits/sec    0    710 KBytes
[  4]  16.00-18.00  sec   184 MBytes   771 Mbits/sec    2    703 KBytes
[  4]  18.00-20.00  sec   185 MBytes   776 Mbits/sec    3    703 KBytes
[  4]  20.00-22.00  sec   184 MBytes   771 Mbits/sec    4    701 KBytes
[  4]  22.00-24.00  sec   185 MBytes   776 Mbits/sec    2    696 KBytes
[  4]  24.00-26.00  sec   184 MBytes   771 Mbits/sec    4    695 KBytes
[  4]  26.00-28.00  sec   184 MBytes   771 Mbits/sec    4    692 KBytes
[  4]  28.00-30.00  sec   209 MBytes   876 Mbits/sec    2    722 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-30.00  sec  2.69 GBytes   771 Mbits/sec   56             sender
[  4]   0.00-30.00  sec  2.69 GBytes   770 Mbits/sec                  receiver
```



