# glibc调试环境搭建

## glibc调试环境的搭建

### 搭建调试环境

1. 安装调试信息

   ```shell
   dpkg --add-architecture i386  
   sudo apt-get update && sudo apt-get upgrade 
   sudo apt-get install libc6-dbg libc6-dbg:i386 
   ```

2. 安装源码

   ```shell
   mkdir ~/glibc_code
   cd glibc_code
   # apt-get source 会将下载的源码下载到glibc_code中
   sudo apt-get source libc6-dev
   ```

### 调试准备

1. 以如下代码为例

   ```c
   #include <stdio.h>
   int main(void)
   {
   	printf("test\n");
   	return 300;
   }
   ```

2. 在printf处断点，step后会提示

   ```shell
   __GI__IO_puts (str=0x555555556004 "test") at ioputs.c:33
   33	ioputs.c: No such file or directory.
   ```

3. 此时，使用info source可以看到

   ```shell
   Current source file is ioputs.c
   Compilation directory is /build/glibc-YYA7BZ/glibc-2.31/libio
   Source language is c.
   Producer is GNU C11 9.3.0 -mtune=generic -march=x86-64 -g -O2 -O3 -std=gnu11 -fgnu89-inline -fmerge-all-constants -frounding-math -fstack-protector-strong -fmath-errno -fPIC -fcf-protection=full -fexceptions -ftls-model=initial-exec -fasynchronous-unwind-tables -fstack-protector-strong -fstack-clash-protection.
   Compiled with DWARF 2 debugging format.
   Does not include preprocessor macro info.
   ```

4. 使用`set substitute-path /build/glibc-YYA7BZ ~/glibc_debug`，就可以显示当前源码

   >如上是最快捷的方法
   >
   >另外一种方法是进入printf函数后，使用`directory ~/glibc_debug/glibc-2.31/stdio-common`指出printf函数源码的文件夹
   >
   >但是这样做，每个glibc函数，只要不在stdtio-common中，都得使用directory命令指出源码文件夹
   >
   >
   >关于set substitude-path命令背后的原理参考gdb手册9.5节