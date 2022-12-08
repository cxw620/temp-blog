# 校园网IPV6 OpenWRT解决方案
+ ## 环境
  +  主路由: N5105主机, docker部署自编译固件, 启用ipv6
  +  AP: 华为路由器开启AP模式
+  ## 需要进行的配置
  +  参考[此教程](https://post.smzdm.com/p/ad9qkz9k/)完成所有操作. 注意: 其中 `ip6tables -t nat -A POSTROUTING -o eth2 -j MASQUERADE` 改为 `ip6tables -t nat -A POSTROUTING -s {IPv6 ULA前缀设置} -o {openwrt下的wan网卡名称} -j MASQUERADE`. 参考配置: ![image](https://user-images.githubusercontent.com/70561268/206371150-5dfea94c-0b82-44ef-9725-c3457cb3ec81.png).
  + `nat6`文件附
  +  网络 -> 静态路由 -> ipv6, 如图 ![image](https://user-images.githubusercontent.com/70561268/206370232-ec6f9185-ad49-443d-92de-9506680474c8.png)
