## Ubuntu网络图标消失

​	今天打开了虚拟机发现无网络连接，右上角网络图标不见了，`ping`不通，使用`ifconfig`查看IP也没有。百度后发现是NetworkManager缓存文件的问题，删除该文件并重启网络服务即可。

```shell
// 停止网络服务
sudo service NetworkManager stop 
 
// 删除NetworkManager缓存文件 
sudo rm /var/lib/NetworkManager/NetworkManager.state 
 
// 重启网络服务 
sudo service NetworkManager start
```

