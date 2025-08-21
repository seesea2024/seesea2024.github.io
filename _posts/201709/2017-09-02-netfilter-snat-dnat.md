## SNAT/DNAT

![IPtable链](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/netfilter_packet_flow.png?raw=true)

图中正菱形的区域是对数据包进行判定转发的地方。在这里，系统会根据IP数据包中的destination ip address中的IP地址对数据包进行分发。如果destination ip adress是本机地址，数据将会被转交给INPUT链。如果不是本机地址，则交给FORWARD链检测。
这也就是说，我们要做的DNAT要在进入这个菱形转发区域之前，也就是在PREROUTING链中做，比如我们要把访问202.103.96.112的访问转发到192.168.0.112上：
```
iptables -t nat -A PREROUTING -d 202.103.96.112 -j DNAT --to-destination 192.168.0.112
```

而SNAT自然是要在数据包流出这台机器之前的最后一个链也就是POSTROUTING链来进行操作
```
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -j SNAT --to-source 58.20.51.66
```
