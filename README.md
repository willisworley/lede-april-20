用户版已经够好用，server 版需要 follow 更新
=
如何编译自己需要的 OpenWrt 固件
-
注意：
-
1. **不**要用 **root** 用户 git 和编译！！！
2. 国内用户编译前最好准备好梯子
3. 默认登陆IP 192.168.1.1, 密码 password

编译命令如下:
-
1. 首先装好 Ubuntu 64bit，推荐  Ubuntu  18 LTS x64

### 用root 用户编译 固件

解决方法：
在/etc/profile中添加：

export FORCE_UNSAFE_CONFIGURE=1

然后编译配置文件使之生效

source /etc/profile


2. 命令行输入 `sudo apt-get update` ，然后输入
`
sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3.5 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf
`

3. 使用 `git clone https://github.com/willisworley/lede-april-20.git` 命令下载好源代码，然后 `cd lede-april-20` 进入目录

   ```bash
   ./scripts/feeds update -a && ./scripts/feeds install -a
   make menuconfig
   ```
   
   ```bash
   cd ./lede && rm -rf ./package/lean/luci-app-dockerman && mkdir -p package/luci-lib-docker && wget https://raw.githubusercontent.com/lisaac/luci-lib-docker/master/Makefile -O package/luci-lib-docker/Makefile && mkdir -p package/luci-app-dockerman && wget https://raw.githubusercontent.com/lisaac/luci-app-dockerman/master/Makefile -O package/luci-app-dockerman/Makefile && ./scripts/feeds update -a && ./scripts/feeds install -a && rm -rf ./.config && cp ./0417-nosamba-x86.config ./.config && rm -rf ./package/lean/luci-app-diskman && mkdir -p package/luci-app-diskman && mkdir -p package/parted && wget https://raw.githubusercontent.com/lisaac/luci-app-diskman/master/Makefile -O package/luci-app-diskman/Makefile && wget https://raw.githubusercontent.com/lisaac/luci-app-diskman/master/Parted.Makefile -O package/parted/Makefile && make -j8 download v=s
    ```
   
    ```bash
    nohup make -j1 V=s >>mylog.txt 2>&1 &
    ```
    
   
4.    
   ```bash
   cd ./lede-april-20 && ./scripts/feeds update -a && ./scripts/feeds install -a && rm -rf ./.config && cp ./0417-nosamba-x86.config ./.config && rm -rf ./package/lean/luci-app-dockerman && mkdir -p package/luci-lib-docker && wget https://raw.githubusercontent.com/lisaac/luci-lib-docker/master/Makefile -O package/luci-lib-docker/Makefile && mkdir -p package/luci-app-dockerman && wget https://raw.githubusercontent.com/lisaac/luci-app-dockerman/master/Makefile -O package/luci-app-dockerman/Makefile
   ```

5. `make -j8 download v=s` 下载dl库（国内请尽量全局科学上网）`nohup make -j8 download v=s >>mylog.txt 2>&1 &`


6. 输入 `make -j1 V=s` （-j1 后面是线程数。第一次编译推荐用单线程）即可开始编译你要的固件了。

如果确定代码.config 能稳定通过 后台编译执行 `nohup make -j1 V=s >>mylog.txt 2>&1 &`

本套代码保证肯定可以编译成功。里面包括了 R20 所有源代码，包括 IPK 的。

你可以自由使用，但源码编译二次发布请注明我的 GitHub 仓库链接。谢谢合作！
=

二次编译：
```bash
cd lede
git pull
./scripts/feeds update -a && ./scripts/feeds install -a
make defconfig
make -j8 download
make -j$(($(nproc) + 1)) V=s
```

如果需要重新配置：
```bash
rm -rf ./tmp && rm -rf .config
make menuconfig
make -j$(($(nproc) + 1)) V=s
```

编译完成后输出路径：/lede/bin/targets

特别提示：
------
1.源代码中绝不含任何后门和可以监控或者劫持你的 HTTPS 的闭源软件，SSL 安全是互联网最后的壁垒。安全干净才是固件应该做到的；

2.如有技术问题需要讨论，欢迎加入 QQ 讨论群：OP共享技术交流群 ,号码 297253733 ，加群链接: 点击链接加入群聊【OP共享技术交流群】：[点击加入](https://jq.qq.com/?_wv=1027&k=5yCRuXL "OP共享技术交流群")

3.想学习OpenWrt开发，但是摸不着门道？自学没毅力？基础太差？怕太难学不会？跟着佐大学OpenWrt开发入门培训班助你能学有所成
报名地址：[点击报名](http://forgotfun.org/2018/04/openwrt-training-2018.html "报名")


------

English Version: How to make your Openwrt firmware.
-
Note:
--
1. DO **NOT** USE **ROOT** USER TO CONFIGURE!!!

2. Login IP is 192.168.1.1 and login password is "password".

Let's start!
---
First, you need a computer with a linux system. It's better to use Ubuntu 18 LTS 64-bit.

Next you need gcc, binutils, bzip2, flex, python3.5+, perl, make, find, grep, diff, unzip, gawk, getopt, subversion, libz-dev and libc headers installed.

To install these program, please login root users and type
`
sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3.5 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib
`
in terminal

Third, logout of root users. And type this `git clone https://github.com/coolsnowwolf/lede` in terminal to clone this source.

After these please type `cd lede` to cd into the source.

Please Run `./scripts/feeds update -a` to get all the latest package definitions
defined in `feeds.conf` / `feeds.conf.default` respectively
and `./scripts/feeds install -a` to install symlinks of all of them into
`package/feeds/` .

Please use `make menuconfig` to choose your preferred
configuration for the toolchain and firmware.

Use `make menuconfig` to configure your image.

Simply running `make` will build your firmware.
It will download all sources, build the cross-compile toolchain,
the kernel and all choosen applications.

To build your own firmware you need to have access to a Linux, BSD or MacOSX system
(case-sensitive filesystem required). Cygwin will not be supported because of
the lack of case sensitiveness in the file system.

## Note: Addition Lean's private package source code in `./package/lean` directory. Use it under GPL v3.

## GPLv3 is compatible with more licenses than GPLv2: it allows you to make combinations with code that has specific kinds of additional requirements that are not in GPLv3 itself. Section 7 has more information about this, including the list of additional requirements that are permitted.


## luci-app-ssr-plus备份

https://github.com/supppig/luci-app-ssr-plus

源自 https://github.com/coolsnowwolf/lede/ 最后一个版本，原版shadowsocksr.lua已被加密，无法安装，已修复，可编译。

神秘代码：

echo 0xDEADBEEF > /etc/config/google_fu_mode
luci-app-ssr-plus
This is a backup repoistory of luci-app-ssr-plus from Lean's OpenWrt project package.

Lean's OpenWrt source:

https://github.com/coolsnowwolf/lede/

Source from tree: 2915c44a11ca0ee40b51ff5d9c18a0da1951e170

Lean's luci-app-ssr-plus source (history):

https://github.com/coolsnowwolf/lede/tree/2915c44a11ca0ee40b51ff5d9c18a0da1951e170/package/lean/luci-app-ssr-plus
