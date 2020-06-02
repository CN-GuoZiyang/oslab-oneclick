# 哈工大操作系统实验环境安装脚本

声明：本仓库用于配置哈工大操作系统实验所需环境，主要包含Bochs虚拟机和 Linux-0.11的源码

本仓库基于[hoverwinter](https://github.com/hoverwinter)的一键配置脚本制作，制作过程中借鉴了[deathking](https://github.com/DeathKing)的一键配置脚本，修复了脚本在最新的Ubuntu与Debian上的一些问题，并添加了Arch系发行版支持，感谢两位大大的工作！

### 我做了什么？

- 增加了对Arch系发行版的支持
- 更新了Bochs模拟器版本到2.6.10（最新版2.6.11在Linux下有重大BUG）
- 更新了Bochs的BIOS和VGABIOS到最新版本
- 修正了Bochs的配置文件（老版本的配置文件无法在新版Bochs上使用）
- 重写了setup脚本，自动安装上了运行所需的依赖
- 借鉴[deathking](https://github.com/DeathKing)增加了还原脚本
- 修正了run、setup等脚本中的一些小错误
- 增加了C语言级调试的CGDB调试器支持

### 环境

本脚本适用于Linux的Debian系发行版和Arch系发行版，Debian系发行版包括但不限于Debian、Deepin 15、Ubuntu及其衍生版本，Arch系发行版包括但不限于Arch Linux、Manjaro及其衍生版本。

本脚本已在Ubuntu 18.04、Ubuntu 20.04、Deepin v15、Elementary OS和Manjaro下完成测试。

**最新：** 完成了在Windows Subsystem for Linux 2（**WSL2**）中的测试！

**注意：** 可能由于apt源的原因，本脚本无法在Deepin v20和UOS上使用。（ **并不是不支持国产！** ）

### 环境安装

首先clone本仓库到某个文件夹下：

```shell
git clone https://gitee.com/cn-guoziyang/oslab.git ~/oslab
```

大小大概25M，请耐心等待。

进入目录并执行安装，Debian系发行版请执行：

```shell
cd ~/oslab
./setup debian
```

Arch系发行版请执行（在此之前请首先添加archlinuxcn的源并获取gpg key，重要！）：

```shell
cd ~/oslab
./setup archlinux
```

注意：请使用普通用户操作，当需要root权限时脚本会自动请求密码。

### 编译

Linux 0.11源码位于`linux-0.11`文件夹下，在该文件夹下进行完成实验后使用如下命令编译：

```shell
make all
```

如果编译较慢，可以启动多核编译支持，例如双核编译的命令为：

```shell
make -j 2
```

编译完成后会在该文件夹下生成`Image`文件，即为编译后的镜像文件。

如果想要清除编译的结果与中间文件，完全重新编译，只需要执行：

```shell
make clean
```

考虑到操作系统试验每次需要重置linux-0.11目录，故配置了重置脚本，在项目目录里：

```shell
# in oslab directory
./init
```

### 运行

进入项目目录，执行

```shell
./run
```

将启动Bochs虚拟机，并自动加载编译完成的`Image`文件，Bochs模拟器上将显示引导日志，最终显示`[/usr/root]#`，则运行成功。

### 调试

调试部分直接使用[hoverwinter](https://github.com/hoverwinter)的文档。

内核调试分为两种模式：汇编级调试和C语言级调试。

汇编级调试需要执行命令：

```shell
./dbg-asm
```

可以用命令help来查看调试系统用的基本命令。更详细的信息请查阅Bochs使用手册。

C语言级调试稍微复杂一些。首先执行如下命令：

```shell
./dbg-c
```

然后再打开一个终端窗口，进入oslab目录后，执行：

```shell
./rungdb
```

新终端窗口中运行的是GDB调试器。关于gdb调试器请查阅GDB使用手册。

除此以外，如果想使用更为强大的CGDB调试器，则在上一步中使用命令：

```shell
./runcgdb
```

来启动cgdb。

### Bochs中的Linux 0.11与宿主机Linux的文件交换

oslab文件夹中的hdc-0.11.img文件，将在在Bochs虚拟机启动后被当作硬盘装载进系统，所有宿主机与虚拟机的文件交换都应通过该文件进行。

**注意**：文件交换时，应当保证虚拟机处于关闭状态，否则会导致镜像文件损坏！

在虚拟机关闭时，在oslab文件夹下运行命令：

```shell
sudo ./mount-hdc
```

这时，hdc文件夹就成为的linux 0.11的根文件夹（`/`），此时即可随意传入传出文件，如lab2中的测试脚本等。

完成传输后，需要使用以下命令卸载该文件系统：

```shell
sudo umount hdc
```

在Bochs linux中进行的修改，不会自动同步到hdc-0.11.img文件，也就是说，关闭后再次启动虚拟机，会加载原本的hdc-0.11.img文件，导致修改丢失，如果需要同步修改到镜像中，需要在Bochs linux的命令行运行以下命令，将文件修改写入镜像：

```shell
sync
```

### 在WSL中的使用

测试环境是Windows 10 2004下的WSL2 Ubuntu，之前版本的WSL1应当也适用。

由于WSL没有GUI界面，需要首先安装X11 Server：

```shell
sudo apt install xorg
```

接着需要配置X11转发，在`~/.bashrc`的最后添加环境变量：
- WSL 1的环境变量设置为`export DISPLAY=localhost:0`
- WSL 2的环境变量设置为：
```shell
export DISPLAY=`cat /etc/resolv.conf | grep nameserver | awk '{print $2}'`:0
```

Windows宿主机中需要安装X11 Client，我使用的是VcXsrv，它的初始配置有一些坑点：
- Multiple Window
- 勾选Disable access controll
- 关闭Windows Defender防火墙

VcXsrv启动后，在WSL中执行`./run`启动Bochs，就会在打开Bochs窗口，测试时效果如下：

[![B9388325092A50BD171127024F894127.md.png](https://images.gitee.com/uploads/images/2020/0523/212531_c82d2413_1910421.png)](https://img.guoziyang.top/image/llG)

左侧的Bochs不是Windows中的Bochs，而是Linux中的Bochs窗口。这样你就可以愉快地在Windows下开发了，强烈推荐用Windows下的vscode连接WSL进行开发，舒服得很。

### 致谢

最终再次感谢[hoverwinter](https://github.com/hoverwinter)和[deathking](https://github.com/DeathKing)两位大大的工作，我也只是做了一些微小的工作，很惭愧。