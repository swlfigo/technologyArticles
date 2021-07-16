


# 使用Brook进行端口转发

```shell
curl -L https://github.com/txthinking/brook/releases/download/v20200909/brook_linux_amd64 -o /usr/bin/brook
chmod +x /usr/bin/brook

```



## 转发

```shell
brook relay -f :中转端口 -t 落地ip:落地端口
```

举例：有一台国内服务器1.1.1.1，搭建了代理的国外服务器ip为2.2.2.2，使用端口为10000，那么运行下面命令：

```shell
brook relay -f :20000 -t 2.2.2.2:10000
```

实现的效果为：访问1.1.1.1的20000端口等同于方位2.2.2.2的10000端口。

### 后台执行

上面命令只对本次执行有效，关闭ssh连接即失效。因此我们需要把上述命令放到linux后台运行。那么上述命令变为：

```shell
nohup brook relay -f :20000 -t 2.2.2.2:10000 > /dev/null 2>&1 &
```