# 饥荒服务器搭建指南

![](http://sylarimage.oss-cn-shenzhen.aliyuncs.com/2020-02-27-033648.jpg)



测试环境: CentOS 7.6 64位

环境的依赖是基于 64 位的,32位不适合

可通过命令来查看版本

```shell
uname -r

3.10.0-327.el7.x86_64   有64就是64位
```

# 1. 首先需要配置依赖环境

```shell
sudo yum update
sudo yum -y install glibc.i686 libstdc++.i686 libcurl4-gnutls-dev.i686 libcurl.i686 screen
```



# 2. 安装SteamCMD 服务器组件

[SteamCMD官网](https://developer.valvesoftware.com/wiki/SteamCMD:zh-cn)

通常都是手动安装SteamCMD

```shell
cd /home && mkdir steamcmd && cd steamcmd

#外国地区可以用这个下载(2选一)
wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz

#中国地区可以用这个下载
curl -sqL "https://media.st.dl.bscstorage.net/client/installer/steamcmd_linux.tar.gz" | tar zxvf -


#下载成功后运行 SteamCMD
./steamcmd.sh
```



![](http://sylarimage.oss-cn-shenzhen.aliyuncs.com/2020-02-27-040133.jpg)

看到上图就是 SteamCMD 打开成功

接下来在Steam> 下输入命令(不需要输入 Steam> !!!!!!,只需要输入 Steam> 后面内容)

```shell
Steam> login anonymous
Steam> force_install_dir /home/dstserver
Steam> app_update 343050 validate
```

第一条，匿名登陆SteamCMD

第二条,	配置服务端下载路径为 '/home/dstserver'

第三条,	下载饥荒服务器

之后服务器会自己下载，等下载完成后
输入`quit`退出`SteamCMD`程序
至此服务器下载安装已经完成，可以开始配置服务器了！



# 3. 服务器配置

首先解决最重要的问题，linux下饥荒的服务器似乎需要的组件跟现在的组件产生了名字上的差错
导致启动服务器会显示缺少关键的组件`libcurl-gnutls.so.4`
因此需要执行下面的命令来解决

```shell
ln -s /usr/lib/libcurl.so.4 /home/dstserver/bin/lib32/libcurl-gnutls.so.4
```

这个坑踩了我半天时间

后面的路径需要根据你上面 `force_install_dir` 来修改，如果是按上面的文件路径这样填就好了

下载完成后，进入 `dstserver` 文件夹

```shell
cd /home/dstserver/bin
```

里面有个 `dontstarve_dedicated_server_nullrenderer` 文件，这个就是服务器开启的工具

饥荒客户端的存档位置如下：

```shell
# Windows
Documents\Klei\DoNotStarveTogether
# Linux
~/.klei/DoNotStarveTogether
# MacOS
~/Documents/Klei/DoNotStarveTogether
```



这个程序还是有其他的启动参数的。主要参数如下：

| 参数                     | 用法                                                         |
| ------------------------ | ------------------------------------------------------------ |
| -persistent_storage_root | 指定存档根目录的位置，必须是绝对目录。默认为 `~/.klei`。     |
| -conf_dir                | 指定配置文件的目录名。默认为 `DoNotStarveTogether`，和上一个参数拼在一起就是你存档的完整位置了，默认为 `~/.klei/DoNotStarveTogether`，所有的存档都在这里。 |
| -cluster                 | 指定启动的世界，默认为 `Cluster_1`。服务端启动时会去找 `//`目录下的 `cluster.ini` 这个配置文件，你的世界名称、密码、游戏模式之类的都是在这里配置的（网上有些教程里用的 `setting.ini`，那个是旧版的）。同理，你的存档文件夹也可以不使用类似 `Cluster_X` 的名字，改成其他什么乱七八糟的都可以，只要启动时指定本参数就行了。 |
| -shard                   | 默认为 `Master`，启动时将此参数指定为 `Cave` 就可以启动洞穴服务器。 |

其他的参数我就不一一说明了，具体的参数列表可以在[这里](http://forums.kleientertainment.com/topic/64743-dedicated-server-command-line-options-guide/)查看。举个栗子：

```shell
# 同时启动主世界服务器和洞穴服务器
./dontstarve_dedicated_server_nullrenderer -console -cluster MyClusterName -shard Master
./dontstarve_dedicated_server_nullrenderer -console -cluster MyClusterName -shard Caves
```

由于命令太长不好记，我们弄成两个脚本来方便启动

```shell
echo "./dontstarve_dedicated_server_nullrenderer -console -persistent_storage_root /home/dstsave -conf_dir dst -cluster World1 -shard Master" > master_start.sh
echo "./dontstarve_dedicated_server_nullrenderer -console -persistent_storage_root /home/dstsave -conf_dir dst -cluster World1 -shard Caves" > cave_start.sh

chmod +x master_start.sh cave_start.sh
```

**注意:**  上面的 `World1` 是存档名字，如果你没放存档到 `/home/dstsave` 下，他会自动帮你生成一个默认的 名字叫`World1` 时间，参数都是默认的

如果你本地有存档想放上去服务器玩，只需要吧存档上传至 `/home/dstsave` 下，然后修改脚本中  `-cluster World1`  命令中 `World1` 这个名字为你的存档名字 ，例如 `Cluster_1`之类的, 注意是2个文件都要改 `master_start.sh` 与 `cave_start.sh`



# 4. 服务器Token配置

首先去 **[Keli账户页面](https://accounts.klei.com/account/game/servers?game=DontStarveTogether)**
登陆你的Steam账号去生成服务器启动密钥**(初次登陆可能页面没跳转，那就再点击一次该链接就可以看到。可能需要科学上网，要不然或许会很慢)**。

![](http://sylarimage.oss-cn-shenzhen.aliyuncs.com/2020-02-27-042415.png)



新建服务器，新建后上面就有你的服务器显示以及你的 `Token`

```shell
$ tree
.
├── Caves # 如果你想要开洞穴的话
│   └── server.ini
├── cluster.ini							<--------- 游戏模式的配置，没有的话自己创建
├── cluster_token.txt       <--------- 需要吧Token放到填到这个文件里，没有的话自己创建
└── Master
    ├── modoverrides.lua
    └── server.ini
```

通常存档就类似上面文件结构，Token按上面所说，填到相应文件，注意这个Token文件与 `Caves` , `Master` 同一级路径,不要放错位置



# 5. Mod配置

配置服务器需要下载的Mod

打开饥荒服务器文件夹,如上面所说的 `force_install_dir` 文件夹,打开 `mods`文件夹

`/home/dstserver/mods`

打开 `dedicated_server_mods_setup.lua` 文件，添加如下内容：

```lua
-- xxx 是社区workshop 的modid
ServerModSetup("xxx")
```

这里注意的是，这个mod 只能是社区里面的 `server_only_mod` ，只能用于服务器的mod,客户端的mod这里没用



在存档 Master 和 Caves 下需要配置Mod的，需要重写  `modoverrides.lua` 这个文件，如果没有需要新建，注意是Master和Caves都需要这个文件，因为分了两个世界,打开 ``modoverrides.lua``

```lua
return {
  ["workshop-xxx"]={ configuration_options={  }, enabled=true },
}
```

这个 `xxx` 就是上面  `dedicated_server_mods_setup.lu` 的 `xxx`mod



# 6. 开启游戏

上面所有东西配置完成后就能开游戏了

到这一步，这个服务器就可以彻底开始玩啦！

输入命令

```shell
cd /home/dstserver/bin
screen -S master
```

之后会进入名叫`master`的控制台输入界面
执行`./master_start.sh`即可开启主世界服务器
按下`Ctrl A`然后按下`Ctrl D`即可回到初始控制台
再次输入`screen -S cave`
并执行`./cave_start.sh`即可开启洞穴服务器
按下`Ctrl A`然后按下`Ctrl D`回到初始控制台

如果要关闭服务器则先输入`screen -r master`进入主世界服务器控制台然后按下`Ctrl C`关闭
洞穴服务器则输入`screen -r cave`进入洞穴服务器控制台然后按下`Ctrl C`关闭

如果重新登录服务器 -r 不了，需要先 `screen -d xxx` , `xxx` 就是这个screen的名字,然后再 `screen -r xxx` 即可

注意的是服务器防火墙需要开放

森林服务器默认端口 10999

洞穴服务器默认端口 10998

如果修改过的话需要开启指定的端口









 