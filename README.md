# linux-
linux内核编译过程和makefile文件生成以及将自定义模块加载到内核中的步骤总结
Linux内核是操作系统的核心，也是操作系统最基本的部分。

    Linux内核的体积结构是单内核的、但是他充分采用了微内核的设计思想、使得虽然是单内核、但工作在模块化的方式下、并且这个模块可以动态装载或卸 载；Linux负责管理系统的进程、内存、设备驱动程序、文件和网络系统，决定着系统的性能和稳定性。如是我们在了解Linux内核的基础上根据自己的需 要、量身定制一个更高效，更稳定的内核，就需要我们手动去编译和配置内核里的各项相关的参数和信息了。

1）编译内核的基本步骤：

1、获取内核源码，解压至/usr/src
# tar xvf linux-3.13.5.tar.xz 
# ln -sv /usr/src/linux-3.13.5 /usr/src/linux
2、配置内核特性(选择一种方法就可以了)
make config：遍历选择所要编译的内核特性
make allyesconfig：配置所有可编译的内核特性
make allnoconfig：并不是所有的都不编译
make menuconfig：这种就是打开一个文件窗口选择菜单
make kconfig(KDE桌面环境下，并且安装了qt开发环境)
make gconfig(Gnome桌面环境，并且安装gtk开发环境)
3、编译内核
# make [-j #] ： #号最多为CPU物理核心总数的两倍，这样会快点哦 
4、安装内核模块
# make modules_install
5、安装内核
# make install
6、验正并测试
# cat /boot/grub/grub.conf
查看新内核是否已经添加, 而后重启系统并测试

2)生成makefile文件

以计算斐波那契序列的程序为例：

1、写一个main.c，fib.c，fib.h文件如下

Main.c

#include<stdio.h>

#include”fib.h”

int main(){

int n=0;

printf(“input n=\n”);

scanf(“%d”,&n);

printf(“fib(%d=%d\n”,n,fib(n));

return 0;

}

fib.h

Int fib(int  n);

fib.c

#include”fib.h”

Int  fib(int  n){

   If(n==0)

      return 0;

   if(n==1||n==2){

       return 1;

   return fib(n-i)+fib(n-2);

}

 

2、需要使用到automake和autoconf两个命令

终端下，使用命令安装：sudo apt-get install autoconf

3、进入源文件目录 执行autoscan 



这时会在目录下生成两个文件 autoscan.log和configure.scan，将configure.Scan改名为configure.ac，同时用gedit打开（下面的文件内容是已经修改过内容以后的，自己对比一下吧）



-----------------------------configure.ac文件开始-----------------------------------------
#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.64])
AC_INIT(first, 1.0, XXXX@XXX.com)  

 AC_INIT([FULL-PACKAGE-NAME], [VERSION], [BUG-REPORT-ADDRESS])
AC_CONFIG_SRCDIR([first.c])
AC_CONFIG_HEADERS([config.h])
AM_INIT_AUTOMAKE(first,1.0)  
# Checks for programs.
AC_PROG_CC
# Checks for libraries.
# Checks for header files.
# Checks for typedefs, structures, and compiler characteristics.
# Checks for library functions.
AC_OUTPUT(Makefile)          
-----------------------------configure.ac文件结束-----------------------------------------
3，新建文件Makefile.am，内容如下：
AUTOMAKE_OPTIONS=foreign
bin_PROGRAMS=first
first_SOURCES=first.c
保存。
4，运行命令aclocal，

命令成功之后，在目录下会产生aclocal.m4和autom4te.cache两个文件。
5，运行命令autoheader

命令成功之后，会在目录下产生config.h.in这个新文件。
6，运行命令autoconf

命令成功之后，会在目录下产生configure这个新文件。
7，运行命令automake --add-missing


输出结果为：
configure.in:8: installing `./install-sh'
configure.in:8: installing `./missing'
Makefile.am: installing `./depcomp'

命令成功之后，会在目录下产生depcomp，install-sh和missing这三个新文件和执行下一步的Makefile.in文件。

8，运行命令  ./configure  
就可以自动生成Makefile。



3）添加内核模块（首先写好模块（.c文件））



建立


 sudo make-C/lilb/modules/$(uname  -r)/build M=$(pwb)



加入模块


Sudo insmod ./hello.ko



移除模块


Sudo rmmod hello



另外打开一个命令行（ctrl+alt+t）可以查看模块添加情况



tail/var/log/kern.log



