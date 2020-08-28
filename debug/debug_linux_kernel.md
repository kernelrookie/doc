# debug linux kernel

## 明确自己需要什么样的调试环境

1. 方便在虚拟机内安装build-essential之类的开发工具

## qemu+gdb

### 调试环境配置步骤

1. 编译调试版本内核（我使用的是5.4版本的内核）

   > 首先，当前的编译选项只能满足我现在的需求，其他很多文档涉及到的编译选项还没用上

   make menuconfig

   打开`Kernel debugging`下的`compile the kernel with debug info`选项

2. busybox：构建initramfs文件系统

   + 为什么需要initramfs文件系统？

     + Linux系统启动阶段，boot loader加载完内核文件vmlinuz后，内核紧接着需要挂载磁盘根文件系统，但如果此时内核没有相应驱动，无法识别磁盘，就需要先加载驱动，而驱动又位于/lib/modules，得挂载根文件系统才能读取，这就陷入了一个两难境地，系统无法顺利启动。于是有了initramfs根文件系统，其中包含必要的设备驱动和工具，boot loader加载initramfs到内存中，内核会将其挂载到根目录/,然后运行/init脚本，挂载真正的磁盘根文件系统。

   + 编译busybox：

     + 下载busybox源码
     + make menuconfig
     + Settings ---> [*] Build static binary (no shared libs)
     + make
     + make install
     + 会安装在_install目录中

   + 创建initramfs

     + mkdir initramfs;cd initramfs

     + cp ../_install/* -rf ./

     + mkdir dev proc sys

     + sudo cp -a /dev/{null, console, tty, tty1, tty2, tty3, tty4} dev/

     + rm linuxrc

     + vim init

       > init文件内容
       >
       > ```shell
       > #!/bin/busybox sh         
       > mount -t proc none /proc  
       > mount -t sysfs none /sys  
       > 
       > exec /sbin/init
       > ```

     + chmod a+x init

     + 打包initramfs

       ```shell
       find . -print0 | cpio --null -ov --format=newc | gzip -9 > ../initramfs.cpio.gz
       ```

3. 开始调试

   1. 启动qemu（启动内核）

      ```shell
      qemu-system-x86_64 -s -kernel /path/to/vmlinux -initrd /path/to/initramfs.cpio.gz -nographic -append "console=ttyS0 nokaslr"
      # -s是-gdb tcp::1234缩写，监听1234端口，在GDB中可以通过target remote localhost:1234连接；
      # -kernel指定编译好的调试版内核，一般在linux内核/arch/架构/boot/bzImage
      # -initrd指定制作的initramfs;
      # -nographic取消图形输出窗口，使QEMU成简单的命令行程序；
      # -append “console=ttyS0″将输出重定向到console，将会显示在标准输出stdio。
      # 另外，4.12以后的内核默认开启了KASLR (Kernel address space layout randomization).会使你获取不到符号表，所以要取消 KASLR。也就是这里的nokaslr。
      ```

   2. 启动gdb

      ```shell
      gdb /path/to/vmlinux
      (gdb) target remote localhost:1234
      ```



## kgdb+virtualbox调试

*在此笔记的同目录下，我放了两份参考文件*

1. virtual box创建一个ubuntu的虚拟机，对virtualbox进行配置，打开串口1,具体操作参考参考文件

   > 记住不要勾选Connect to existing pipe/socket，因为我们并没有自己创建一个虚拟的串口文件(就是一个pipe文件)
   >
   > 不勾选这个选项，virtualbox会根据Path/Address创建模拟的串口文件

2. 编译调试版本的内核

   1. 从虚拟机中拷贝/boot/config-\`uname -r\`文件到内核源码中
   2. make menuconfig需要打开的选项，参考参考文件中的KGDB with VirtualBox的配置

3. 创建一个virtualbox和主机共享的文件夹，把编译好的内核放进去。make modules_install, make install之完成虚拟机内ubuntu内核的替换

   > 1. 这里不要模仿有些博客使用NFS什么的共享文件，太麻烦
   >2. ubuntu替换内核之后，默认启动的就是新内核了，不需要做什么配置
   > 3. virtualbox的共享文件夹可能会有权限的问题，参考下面problems中的解决方法

4. kgdb需要开机进行配置，对/etc/default/grub做如下配置

   > GRUB_CMDLINE_LINUX_DEFAULT="quiet splash kgdboc=ttyS0,115200 kgdbwait nokaslr"
   >
   > quiet splash：原先就有的
   >
   > kgdb=ttyS0,115200：指出了使用串口1,115200的波特率。之后在选择使用virtualbox的串口时，根据ttyS0我们需要使用串口1
   >
   > kgdbwait：表示内核启动会暂停，等待调试端发出的指令再继续
   >
   > nokaslr：因为我编译内核时忘了关闭KASLR这个选项，我就选择在这里关闭KASLR

5. 将物理机中的gdb和虚拟机的内核连接起来

   1. socat -d -d /path/to/serial pty，查看使用的pty设备

   2. gdb

      > file vmlinux(虚拟机中内核对应的vmliux)
      >
      > set serial baud 115200
      >
      > target remote /dev/pts/<num>

### problems

1. 解决virtualbox下共享文件夹权限问题

   > 使用virtualbox最方便的host-guest交换文件方案莫过于共享文件夹功能了。
   >
   > 比如host有个叫git的文件夹，可以直接将此文件夹设置为共享文件夹并自动mount，这样，每次在虚拟机一开机就看到这个文件夹被挂载为/media/sf_git
   >
   > 但用非root用户访问这个文件夹时会遇到权限不足问题。根源在于这个自动mount的文件夹的所有者为root，所属的组是vboxsf，并且只有这两个用户有访问权限
   >
   > 解决方法也很简单，只需要将当期登录用户加入到vboxsf组就行了
   >
   > ```
   > sudo usermod -aG vboxsf $(whoami)
   > ```
   >
   > 记得要重启一次系统组设置才能生效

## 调试技巧

1. 在函数cmdline_proc_show设置断点，虚拟机中运行cat /proc/cmdline命令即会触发

   > virtualbox调试的话，在虚拟机中echo g > /proc/sysrq-trigger也会暂停执行虚拟机，然后就可以用物理机中gdb了

2. 分析系统调用：strace

3. ftrace, trace-cmd, kernelshark

   trace-cmd是对ftrace的封装，是一个可以用来跟踪linux内核中事件(event)发生的时间、次数等的工具。

   首先需要安装trace-cmd:

   ```
   $sudo aptitude install trace-cmd
   ```

   使用也非常简单:

   ```
   $sudo trace-cmd record -e sched_switch
   ```

   -e 参数指出需要跟踪的事件，这里的sched_switch指的是进程切换，还可以是hrtimer、irq等，例如:

   ```
   $sudo trace-cmd record -e sched_switch -e hrtimer*
   ```

   命令输完回车后，trace-cmd就开始记录内核中事件的发生了，ctrl+C 停止跟踪，此时会生成一个trace.dat文件，相当于一个log吧。

   trace.dat里的内容不易读，trace-cmd提供分析trace.dat的功能:

   ```
   $trace-cmd report
   ```

   就可以看到所跟踪的事件发生的情况了.

   也可以重定向到一个文本文件中再做分析，比如:

   ```
   $trace-cmd report > report.txt
   ```

   跟踪的报告就存入report.txt中了，可以用Perl等来处理。

   注:使用trace-cmd需要内核开启ftrace，即在编译内核make menuconfig时，选择下面的选项:

   Kernel Hacking -> Tracers -> Kernel Function Tracer

   kernelshark提供了对trace.dat的可视化分析，首先安装:

   ```
   $sudo aptitude install kernelshark
   ```

   然后在trace.dat所在的目录输入:

   ```
   $kernelshark
   ```

   即可看到图形化界面了:
