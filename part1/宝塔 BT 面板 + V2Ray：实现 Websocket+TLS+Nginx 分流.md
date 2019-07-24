> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://injoy.work/archives/ed4e432a.html



一台 VPS（系统 Ubuntu 16.04），一个域名（提前做好解析），SSH 工具

安装 V2Ray
------------------------------

SSH 连接上远程 VPS 后，下载安装脚本：  

```shell
wget https://install.direct/go.sh 
```



然后执行脚本安装 V2Ray：  

```shell
sudo bash go.sh
```


在安装完 V2Ray 之后，修改配置文件重启 V2Ray 即可，配置文件路径为 / etc/v2ray/config.json

以下是 V2Ray 可使用的命令：  

```shell
sudo service v2ray start #启动 V2Ray  
sudo service v2ray stop #停止运行 V2Ray  
sudo service v2ray restart #重启 V2Ray  
sudo service v2ray status #查看 V2Ray 状态  
```

安装宝塔 BT 面板
----------------------------------

根据 VPS 具体系统选择相应一键安装脚本，本教程以 Ubuntu 为例：

```shell
wget -O install.sh http://download.bt.cn/install/install-ubuntu.sh && sudo bash install.sh
```



安装完成后，记录终端自动生成的账户密码，然后浏览器登录 BT 面板操作，建议初次登陆后修改用户名和密码。

安装 LNMP 环境 + Nignx 配置
-------------------------------------------------------------

- 登录BT面板后，可一键部署源码，选择安装LNMP环境；
- 一切都安装完后，点击「网站」——「添加站点」

![](https://sylarimage.oss-cn-shenzhen.aliyuncs.com/20190704144852.png)

成功添加完站点后，点击「设置」——SSL——Let’s Encrypt，成功申请SSL后，保存

![](https://sylarimage.oss-cn-shenzhen.aliyuncs.com/20190704144910.png)



再点击站点「设置」的「配置文件」选项，在ssl最后一个}前添加如下代码：

```json
location /ws {   
     proxy_redirect off;
     proxy_http_version 1.1;
     proxy_set_header Upgrade $http_upgrade;
     proxy_set_header Connection "upgrade";
     proxy_set_header Host $http_host; 
     proxy_pass http://127.0.0.1:你的V2Ray端口; 
} 
```



修改 V2Ray 配置文件
-------------------------------------------

*   打开面板「文件」，在路径中输入_etc_v2ray/，编辑一下文件，并保存 
  
    ```shell
    
    {
      "log" : {
        "access": "/var/log/v2ray/access.log",
        "error": "/var/log/v2ray/error.log",
        "loglevel": "warning"
      },
      "inbound": {
        "port": 9000, //(v2ray端口,防火墙需要开启,nginx需要填充)
        "listen": "127.0.0.1",
        "protocol": "vmess",
        "settings": {
          "clients": [
            {
              "id": "eb950add-608e-409d-937f-e797324387093z", //你的UUID， 此ID需与客户端保持一致
              "level": 1,
              "alterId": 64 //此ID也需与客户端保持一致
            }
          ]
        },
       "streamSettings":{
          "network": "ws",
          "wsSettings": {
               "path": "/ws" //与nginx配置相关
          }
       }
      },
      "outbound": {
        "protocol": "freedom",
        "settings": {}
      },
      "outboundDetour": [
        {
          "protocol": "blackhole",
          "settings": {},
          "tag": "blocked"
        }
      ],
      "routing": {
        "strategy": "rules",
        "settings": {
          "rules": [
            {
              "type": "field",
              "ip": [
                "0.0.0.0/8",
                "10.0.0.0/8",
                "100.64.0.0/10",
                "127.0.0.0/8",
                "169.254.0.0/16",
                "172.16.0.0/12",
                "192.0.0.0/24",
                "192.0.2.0/24",
                "192.168.0.0/16",
                "198.18.0.0/15",
                "198.51.100.0/24",
                "203.0.113.0/24",
                "::1/128",
                "fc00::/7",
                "fe80::/10"
              ],
              "outboundTag": "blocked"
            }
          ]
        }
      }
    }
    
    ```
    
    
    
*   重启 V2Ray 服务  
  
    ```shell
    service v2ray restart  
    service v2ray status #通过该命令，可查看 v2ray 是否运行成功
    ```
    
    

1.  开启 CDN，推荐 [CloudFlare](https://www.cloudflare.com/)
    *   优点：隐藏真实 IP 地址
    *   缺点：国内解析速度较慢，会影响速度
2.  安装 BBR：推荐[一键安装最新内核并开启 BBR 脚本](https://teddysun.com/489.html)

iOS
-----------------

*   Shadowrocket：选择 Vmess 协议，「混淆」选择「websocket」并添加路径 / ws (TLS开启也行) 
    ![](https://sylarimage.oss-cn-shenzhen.aliyuncs.com/20190704143953.png)

macOS
-----------------------

`V2RayX`，配置如下：

![](https://sylarimage.oss-cn-shenzhen.aliyuncs.com/20190704144028.png)



![](https://sylarimage.oss-cn-shenzhen.aliyuncs.com/20190704144100.png)



![](https://sylarimage.oss-cn-shenzhen.aliyuncs.com/20190704144115.png)



通过 Websocket+TLS+Nginx 能够在最大程度上隐藏 VPS 的地址，同时伪装成正常的网站访问，从而降低 IP 被 Ban 的风险；通过这个方法，同样也能够使已经被封的 IP 重新使用。