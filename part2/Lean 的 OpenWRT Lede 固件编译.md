> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://blog.rhyland.top/compile-openwrt/


编译 OpenWRT 用 root 用户可能会出错，所以这边要新建一个普通用户来进行编译

```shell
sudo useradd -r -m -s /bin/bash lede
```

创建一个用户名为 lede 的用户并指定 bash 为终端

```lede
sudo passwd lede
```

给用户 lede 添加一个密码

```
sudo chmod +w /etc/sudoers 
sudo vim /etc/sudoers
```

编辑用户权限

![](http://sylarimage.oss-cn-shenzhen.aliyuncs.com/2019-11-21-073048.jpg)  
在 User privilege specification 加入与 root 用户相同的权限

```
:wq
```

保存并退出 vim 编辑器

```
sudo chmod -w /etc/sudoers
```

去掉 w 权限

3.  下载源代码   [Github 仓库链接](https://github.com/coolsnowwolf/lede "Github仓库链接")

```
su lede
```

切换到刚才创建的新用户（输入密码不会显示）

```
sudo apt-get update

//如果出现 ， 需要加权限 fatal: could not create work tree dir 'project'.: 
sudo chmod o+w dirname
```

升级软件源

```shell
sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git
```

安装编译所需软件包

```shell
git clone https://github.com/coolsnowwolf/lede
```

克隆源码仓库

```shell
./scripts/feeds update -a
./scripts/feeds install -a
```

升级、安装附属包

```shell
make menuconfig
```

进入菜单开始配置系统固件  
（空格选定，上下左右控制方向）

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g95p4uvjg5j316q0s60we.jpg)  
Target System 选择 x86

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g95p4yktpzj316q0s6af5.jpg)  
**Target Image 里面一定要选择`squashfs`！**

![](https://i.loli.net/2019/09/03/e6yraX2tmNPiYku.jpg)  
LuCI – Application 可以定制你要的系统插件，例如（不可描述）等

完成之后可以`Exit`保存退出

```shell
make -j1 V=s 
```

开始编译，-j1 是代表用一个线程，V=s 代表输出全部过程  
