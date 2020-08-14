##  两个namespace三台物理机benchmark配置

#### 配置

##### 全局配置

在客户端上配置veth的转发

```
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -s 106.2.0.0/16 -o bond0 -j MASQUERADE
iptables -t filter -P FORWARD ACCEPT
```

##### 配置namespace1

veth对1&2

```
ip netns add ns1
ip link add veth1 type veth peer name veth2
ip link set veth2 netns ns1
ifconfig veth1 106.2.0.1 netmask 255.255.0.0 up
ip netns exec ns1 ifconfig veth4 106.2.0.2 netmask 255.255.0.0 up
ip netns exec ns1 route add default gw 106.2.0.1
route add -host 106.2.0.2  dev veth1
```

##### 配置namespace2

veth对3&4

```shell
ip netns add ns2
ip link add veth3 type veth peer name veth4
ip link set veth4 netns ns2
ifconfig veth3 106.2.0.3 netmask 255.255.0.0 up
ip netns exec ns2 ifconfig veth4 106.2.0.4 netmask 255.255.0.0 up
ip netns exec ns2 route add default gw 106.2.0.3
route add -host 106.2.0.4  dev veth3
```

##### 三台机器间的转发配置

###### namespace1

```
root@163:~# ip netns exec ns1 ./bench_io_select_udp_tun --tun-address 10.2.0.1 --address 192.168.100.2 -p 8888
root@54:~# ./bench_io_select_udp_tun --tun-address 10.2.0.2 --address 192.168.100.1 -p 8888
root@163:~# ip netns exec ns1 route add -host 192.168.200.4/32 dev 0
root@54:~# iptables -t nat -A POSTROUTING -s 10.2.0.1 -o bond1 -j MASQUERADE
```

###### namspace2

```
root@163:~# ip netns exec ns2 ./bench_io_select_udp_tun --tun-address 10.3.0.1 --address 192.168.100.2 -p 9999
root@54:~# ./bench_io_select_udp_tun --tun-address 10.3.0.2 --address 192.168.100.1 -p 9999
root@163:~# ip netns exec ns2 route add -host 192.168.200.4/32 dev 0
root@54:~# iptables -t nat -A POSTROUTING -s 10.3.0.1 -o bond1 -j MASQUERADE
```



#### 连接

```
 root@56:~# iperf3 -s -p 5000
 root@163:~# ip netns exec ns1 iperf3 -c 192.168.200.4 -p 5000
 
 root@56:~# iperf3 -s -p 8000
 root@163:~# ip netns exec ns2 iperf3 -c 192.168.200.4 -p 8000
```



#### 实验结果

当ns1网络与ns2网络同时开启时，两段网络网速几乎均等

```
root@NASG:~# ip netns exec ns2 iperf3 -c 192.168.200.4 -p 8000 -t 30 -i 2
Connecting to host 192.168.200.4, port 8000
[  4] local 10.3.0.1 port 57228 connected to 192.168.200.4 port 8000
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-2.00   sec   159 MBytes   669 Mbits/sec   23    431 KBytes
[  4]   2.00-4.00   sec   164 MBytes   687 Mbits/sec    1    654 KBytes
[  4]   4.00-6.00   sec   165 MBytes   692 Mbits/sec    5    613 KBytes
[  4]   6.00-8.00   sec   181 MBytes   760 Mbits/sec   44    378 KBytes
[  4]   8.00-10.00  sec   218 MBytes   912 Mbits/sec    0    689 KBytes
[  4]  10.00-12.00  sec   220 MBytes   923 Mbits/sec   27    557 KBytes
[  4]  12.00-14.00  sec   221 MBytes   928 Mbits/sec    2    600 KBytes
[  4]  14.00-16.00  sec   165 MBytes   692 Mbits/sec  325    576 KBytes
[  4]  16.00-18.00  sec   174 MBytes   729 Mbits/sec   94    467 KBytes
[  4]  18.00-20.00  sec   219 MBytes   918 Mbits/sec    4    520 KBytes
[  4]  20.00-22.00  sec   212 MBytes   891 Mbits/sec    3    550 KBytes
[  4]  22.00-24.00  sec   178 MBytes   744 Mbits/sec   18    537 KBytes
[  4]  24.00-26.00  sec   188 MBytes   786 Mbits/sec   41    422 KBytes
[  4]  26.00-28.00  sec   172 MBytes   724 Mbits/sec    0    663 KBytes
[  4]  28.00-30.00  sec   164 MBytes   687 Mbits/sec    2    645 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-30.00  sec  2.73 GBytes   783 Mbits/sec  589             sender
[  4]   0.00-30.00  sec  2.73 GBytes   782 Mbits/sec                  receiver

iperf Done.


root@NASG:~# ip netns exec ns1 iperf3 -c 192.168.200.4 -p 5000 -t 30 -i 2
Connecting to host 192.168.200.4, port 5000
[  4] local 10.2.0.1 port 41240 connected to 192.168.200.4 port 5000
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-2.00   sec   218 MBytes   915 Mbits/sec    1    526 KBytes
[  4]   2.00-4.00   sec   219 MBytes   917 Mbits/sec   10    527 KBytes
[  4]   4.00-6.00   sec   221 MBytes   926 Mbits/sec    1    527 KBytes
[  4]   6.00-8.00   sec   192 MBytes   804 Mbits/sec  434    352 KBytes
[  4]   8.00-10.00  sec   166 MBytes   695 Mbits/sec    1    527 KBytes
[  4]  10.00-12.00  sec   165 MBytes   690 Mbits/sec    4    538 KBytes
[  4]  12.00-14.00  sec   180 MBytes   753 Mbits/sec  511    316 KBytes
[  4]  14.00-16.00  sec   220 MBytes   923 Mbits/sec    0    660 KBytes
[  4]  16.00-18.00  sec   199 MBytes   834 Mbits/sec   72    442 KBytes
[  4]  18.00-20.00  sec   169 MBytes   708 Mbits/sec   22    587 KBytes
[  4]  20.00-22.00  sec   165 MBytes   692 Mbits/sec  296    396 KBytes
[  4]  22.00-24.00  sec   220 MBytes   923 Mbits/sec    0    702 KBytes
[  4]  24.00-26.00  sec   182 MBytes   765 Mbits/sec  409    324 KBytes
[  4]  26.00-28.00  sec   222 MBytes   933 Mbits/sec    0    668 KBytes
[  4]  28.00-30.00  sec   218 MBytes   912 Mbits/sec    1    696 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-30.00  sec  2.88 GBytes   826 Mbits/sec  1762             sender
[  4]   0.00-30.00  sec  2.88 GBytes   825 Mbits/sec                  receiver

iperf Done.

```



当ns1网络先开启10s，ns2网络再开启时，有明显竞争关系，ns1网络结束后，ns2网络速度明显提高

```
root@NASG:~# ip netns exec ns2 iperf3 -c 192.168.200.4 -p 8000 -t 30 -i 2
Connecting to host 192.168.200.4, port 8000
[  4] local 10.3.0.1 port 57566 connected to 192.168.200.4 port 8000
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-2.00   sec   138 MBytes   581 Mbits/sec   32    305 KBytes
[  4]   2.00-4.00   sec   143 MBytes   599 Mbits/sec    0    557 KBytes
[  4]   4.00-6.00   sec   147 MBytes   615 Mbits/sec    2    597 KBytes
[  4]   6.00-8.00   sec   129 MBytes   541 Mbits/sec   10    653 KBytes
[  4]   8.00-10.00  sec   175 MBytes   734 Mbits/sec    2    629 KBytes
[  4]  10.00-12.00  sec   208 MBytes   870 Mbits/sec    3    657 KBytes
[  4]  12.00-14.00  sec   220 MBytes   923 Mbits/sec   10    685 KBytes
[  4]  14.00-16.00  sec   219 MBytes   918 Mbits/sec   37    745 KBytes
[  4]  16.00-18.00  sec   221 MBytes   926 Mbits/sec   38    438 KBytes
[  4]  18.00-20.00  sec   222 MBytes   935 Mbits/sec   15    410 KBytes
[  4]  20.00-22.00  sec   218 MBytes   912 Mbits/sec    0    708 KBytes
[  4]  22.00-24.00  sec   219 MBytes   918 Mbits/sec    1    734 KBytes
[  4]  24.00-26.00  sec   214 MBytes   897 Mbits/sec    1    751 KBytes
[  4]  26.00-28.00  sec   218 MBytes   912 Mbits/sec    5    555 KBytes
[  4]  28.00-30.00  sec   220 MBytes   923 Mbits/sec   57    631 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-30.00  sec  2.84 GBytes   813 Mbits/sec  213             sender
[  4]   0.00-30.00  sec  2.84 GBytes   813 Mbits/sec                  receiver

iperf Done.


root@NASG:~# ip netns exec ns1 iperf3 -c 192.168.200.4 -p 5000 -t 30 -i 2
Connecting to host 192.168.200.4, port 5000
[  4] local 10.2.0.1 port 41584 connected to 192.168.200.4 port 5000
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-2.00   sec   163 MBytes   683 Mbits/sec   23    338 KBytes
[  4]   2.00-4.00   sec   162 MBytes   680 Mbits/sec   19    470 KBytes
[  4]   4.00-6.00   sec   162 MBytes   680 Mbits/sec    3    470 KBytes
[  4]   6.00-8.00   sec   166 MBytes   697 Mbits/sec    8    470 KBytes
[  4]   8.00-10.00  sec   163 MBytes   683 Mbits/sec   12    471 KBytes
[  4]  10.00-12.00  sec   164 MBytes   686 Mbits/sec    0    471 KBytes
[  4]  12.00-14.00  sec   166 MBytes   697 Mbits/sec    0    471 KBytes
[  4]  14.00-16.00  sec   161 MBytes   674 Mbits/sec    0    471 KBytes
[  4]  16.00-18.00  sec   166 MBytes   696 Mbits/sec    0    471 KBytes
[  4]  18.00-20.00  sec   164 MBytes   687 Mbits/sec    0    471 KBytes
[  4]  20.00-22.00  sec   181 MBytes   761 Mbits/sec    0    471 KBytes
[  4]  22.00-24.00  sec   203 MBytes   852 Mbits/sec    0    471 KBytes
[  4]  24.00-26.00  sec   203 MBytes   853 Mbits/sec    0    471 KBytes
[  4]  26.00-28.00  sec   202 MBytes   849 Mbits/sec    0    471 KBytes
[  4]  28.00-30.00  sec   204 MBytes   856 Mbits/sec    0    471 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-30.00  sec  2.57 GBytes   736 Mbits/sec   65             sender
[  4]   0.00-30.00  sec  2.57 GBytes   735 Mbits/sec                  receiver

iperf Done.

```