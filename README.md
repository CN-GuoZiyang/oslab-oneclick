# 哈工大操作系统实验环境安装脚本

声明：本仓库用于配置哈工大操作系统实验所需环境，主要包含Bochs虚拟机和 Linux-0.11的源码

本仓库基于[hoverwinter](https://github.com/hoverwinter)的一键配置脚本制作，制作过程中借鉴了[deathking](https://github.com/DeathKing)的一键配置脚本，修复了脚本在最新的Ubuntu与Debian上的一些问题，感谢两位大大的工作！

### 我做了什么？

- 更新了Bochs模拟器版本到2.6.10（最新版2.6.11在Linux下有重大BUG）
- 更新了Bochs的BIOS和VGABIOS到最新版本
- 修正了Bochs的配置文件（老版本的配置文件无法在新版Bochs上使用）
- 重写了setup脚本，自动安装上了运行所需的依赖
- 借鉴[deathking](https://github.com/DeathKing)增加了还原脚本
- 修正了run、setup等脚本中的一些小错误

### 环境

本脚本适用于Linux的Debian系发行版，包括但不限于Debian、Deepin、Ubuntu及其衍生版本。

本脚本已在Ubuntu、Deepin和Elementary OS下完成测试。

### 环境安装

首先clone本仓库到某个文件夹下：

```shell
git clone https://gitee.com/cn-guoziyang/oslab.git ~/oslab
```

大小大概25M，请耐心等待。

进入目录并执行：

```shell
cd ~/oslab
./setup
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

### 致谢

最终再次感谢[hoverwinter](https://github.com/hoverwinter)和[deathking](https://github.com/DeathKing)两位大大的工作，我也只是做了一些微小的工作，很惭愧。