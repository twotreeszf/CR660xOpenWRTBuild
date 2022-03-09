#OpenWrt路由器系统编译-Ubuntu
===================================================================================================

[](#源码 "源码")源码[](#源码)
---------------------

仓库地址：[https://github.com/coolsnowwolf/lede](https://github.com/coolsnowwolf/lede) ，感谢lean大神提供全网最优的编译库  
openwrt packages [https://github.com/openwrt/packages](https://github.com/openwrt/packages)  
kenzok8提供了非常丰富的插件和皮肤 [https://github.com/kenzok8/openwrt-packages](https://github.com/kenzok8/openwrt-packages)

[](#注意 "注意")注意[](#注意)
---------------------

1.  **不**要用 **root** 用户进行编译！！！
2.  国内用户编译前最好准备好梯子
3.  默认登陆IP 192.168.1.1 密码 password

[](#开始首次编译 "开始首次编译")开始首次编译[](#开始首次编译)
-------------------------------------

### [](#1-安装Ubuntu-64bit， "1. 安装Ubuntu 64bit，")1\. 安装Ubuntu 64bit，[](#1-安装Ubuntu-64bit，)

推荐安装 Ubuntu 20.04 LTS x64

### [](#2-更新核心库 "2. 更新核心库")2\. 更新核心库[](#2-更新核心库)

```
sudo apt-get update 
```
### [](#3-安装编译所需核心库 "3. 安装编译所需核心库")3\. 安装编译所需核心库[](#3-安装编译所需核心库)

```
sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget curl swig rsync
``` 
### [](#4-使用git克隆lean仓库 "4. 使用git克隆lean仓库")4\. 使用git克隆lean仓库[](#4-使用git克隆lean仓库)

```
git clone https://github.com/coolsnowwolf/lede
```

### [](#5-进入到lede目录 "5. 进入到lede目录")5\. 进入到lede目录[](#5-进入到lede目录)

```
cd lede
```    

### [](#6-添加其他插件到packages "6. 添加其他插件到packages")6\. 添加其他插件到packages[](#6-添加其他插件到packages)

编辑lede目录中的feeds.conf.default文件，在尾行加入其他插件库

```
src-git kenzo https://github.com/kenzok8/openwrt-packages
src-git small https://github.com/kenzok8/small
```

### [](#7-更新插件库 "7. 更新插件库")7\. 更新插件库[](#7-更新插件库)

```
./scripts/feeds update -a
```

### [](#8-安装插件 "8. 安装插件")8\. 安装插件[](#8-安装插件)

```
./scripts/feeds install -a
```

### [](#9-配置编译项 "9. 配置编译项")9\. 配置编译项[](#9-配置编译项)

执行下面命令进入界面话操作，选择相应CPU指令集架构和相应型号的设备
```
make menuconfig
```
### [](#10-下载dl库 "10. 下载dl库")10\. 下载dl库[](#10-下载dl库)

```
make -j8 download V=s
```
### [](#11-开始编译 "11.开始编译")11.开始编译[](#11-开始编译)

```
make -j1 V=s
```

[](#二次编译 "二次编译")二次编译[](#二次编译)
-----------------------------

### [](#不更改插件仅仅升级 "不更改插件仅仅升级")不更改插件仅仅升级[](#不更改插件仅仅升级)

```
cd lede
git pull
./scripts/feeds update -a && ./scripts/feeds install -a
make defconfig
make -j8 download
make -j$(($(nproc) + 1)) V=s
```

### [](#重新配置 "重新配置")重新配置[](#重新配置)

```
rm -rf ./tmp && rm -rf .config
make menuconfig
make -j$(($(nproc) + 1)) V=s
```

[](#固件位置 "固件位置")固件位置[](#固件位置)
-----------------------------

编译完成后，固件存放在lede目录的bin/targets目录中，子目录对应了相应的型号，子目录有分别有不同的固件包：

*   squashfs-factory后缀结尾的是过渡固件，适用于其他系统刷入到openwrt
*   quashfs-sysupgrade后缀结尾的是升级固件，适用于openwrt升级
*   initramfs-kernel 是放在内存RAM中的rootfs 映像文件，跟kernel放在一起。一般来说用不到initramfs-kernel.bin来刷机，因为启动后，所有的配置在路由器重启后都不能保留。这种配置在最初的 OpenWRT/LEDE 移植工作中很有用

> squashfs 是一种文件系统，适用于嵌入式设备  
> [initramfs-kernel](https://unix.stackexchange.com/questions/385863/wrt-openwrt-lede-initramfs)

[](#设备型号 "设备型号")设备型号[](#设备型号)
-----------------------------

> 目前手上仅仅只有这两款设备

### [](#CR6608 "CR6608")CR6608[](#CR6608)

#### [](#CPU指令架构 "CPU指令架构")CPU指令架构[](#CPU指令架构)

ramips-mt7621

#### [](#子选项 "子选项")子选项[](#子选项)

CR660X

### [](#N1 "N1")N1[](#N1)

#### [](#CPU指令架构-1 "CPU指令架构:")CPU指令架构:[](#CPU指令架构-1)

OEMU\_ARM\_Virtual\_Machine

#### [](#子选项-1 "子选项")子选项[](#子选项-1)

ARMv8\_multiplatform

> N1详细参考 [https://zhujitips.com/2410](https://zhujitips.com/2410)

[](#喜好插件 "喜好插件")喜好插件[](#喜好插件)
-----------------------------

package: ppp-mod-pptp #可以通过网络接口VPN连接到公司内部网络,避免和ssrp冲突

Include BBR CCA #BBR拥塞控制算法提升TCP网络性能

luci-app-statistics #流量监控工具

luci-app-vlmcsd #KMS服务器设置

luci-app-ssr-plus #SSR科学上网Plus+

luci-app-ttyd #网页终端命令行

Include DNSProxy #DNS防污染 Proxy

luci-app-unblockmusic #解锁网易云灰色歌曲3合1新版本

> 插件说明参考[https://www.right.com.cn/forum/thread-344825-1-1.html](https://www.right.com.cn/forum/thread-344825-1-1.html)
