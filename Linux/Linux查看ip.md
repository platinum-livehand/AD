## 查看ip

```c++
root@localhost:~# ifconfig
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.235.128  netmask 255.255.255.0  broadcast 192.168.235.255
        inet6 fe80::20c:29ff:fe3a:b210  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:3a:b2:10  txqueuelen 1000  (以太网)
        RX packets 1306  bytes 1128747 (1.1 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1188  bytes 262666 (262.6 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (本地环回)
        RX packets 12287  bytes 888857 (888.8 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 12287  bytes 888857 (888.8 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```

