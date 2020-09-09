# 解析IOCTL参数列表中的cmd
1. 精简后相关源码，保留了重要注释

   ```c
   /* ioctl command encoding: 32 bits total, command in lower 16 bits,
    * size of the parameter structure in the lower 14 bits of the
    * upper 16 bits.
    * Encoding the size of the parameter structure in the ioctl request
    * is useful for catching programs compiled with old versions
    * and to avoid overwriting user space outside the user buffer area.
    * The highest 2 bits are reserved for indicating the ``access mode''.
    * NOTE: This limits the max parameter size to 16kB -1 !
    */
    
   #define _IOC_NRBITS	8
   #define _IOC_TYPEBITS	8
   
   # define _IOC_SIZEBITS	14
   
   # define _IOC_DIRBITS	2
   
   #define _IOC_NRMASK	((1 << _IOC_NRBITS)-1)
   #define _IOC_TYPEMASK	((1 << _IOC_TYPEBITS)-1)
   #define _IOC_SIZEMASK	((1 << _IOC_SIZEBITS)-1)
   #define _IOC_DIRMASK	((1 << _IOC_DIRBITS)-1)
   
   #define _IOC_NRSHIFT	0
   #define _IOC_TYPESHIFT	(_IOC_NRSHIFT+_IOC_NRBITS)
   #define _IOC_SIZESHIFT	(_IOC_TYPESHIFT+_IOC_TYPEBITS)
   #define _IOC_DIRSHIFT	(_IOC_SIZESHIFT+_IOC_SIZEBITS)
   
   # define _IOC_NONE	0U
   
   # define _IOC_WRITE	1U
   
   # define _IOC_READ	2U
   
   #define _IOC(dir,type,nr,size) \
   	(((dir)  << _IOC_DIRSHIFT) | \
   	 ((type) << _IOC_TYPESHIFT) | \
   	 ((nr)   << _IOC_NRSHIFT) | \
   	 ((size) << _IOC_SIZESHIFT))
   
   #define _IOC_TYPECHECK(t) (sizeof(t))
   
   /*
    * Used to create numbers.
    *
    * NOTE: _IOW means userland is writing and kernel is reading. _IOR
    * means userland is reading and kernel is writing.
    */
   #define _IO(type,nr)		_IOC(_IOC_NONE,(type),(nr),0)
   #define _IOR(type,nr,size)	_IOC(_IOC_READ,(type),(nr),(_IOC_TYPECHECK(size)))
   #define _IOW(type,nr,size)	_IOC(_IOC_WRITE,(type),(nr),(_IOC_TYPECHECK(size)))
   #define _IOWR(type,nr,size)	_IOC(_IOC_READ|_IOC_WRITE,(type),(nr),(_IOC_TYPECHECK(size)))
   #define _IOR_BAD(type,nr,size)	_IOC(_IOC_READ,(type),(nr),sizeof(size))
   #define _IOW_BAD(type,nr,size)	_IOC(_IOC_WRITE,(type),(nr),sizeof(size))
   #define _IOWR_BAD(type,nr,size)	_IOC(_IOC_READ|_IOC_WRITE,(type),(nr),sizeof(size))
   
   /* used to decode ioctl numbers.. */
   #define _IOC_DIR(nr)		(((nr) >> _IOC_DIRSHIFT) & _IOC_DIRMASK)
   #define _IOC_TYPE(nr)		(((nr) >> _IOC_TYPESHIFT) & _IOC_TYPEMASK)
   #define _IOC_NR(nr)		(((nr) >> _IOC_NRSHIFT) & _IOC_NRMASK)
   #define _IOC_SIZE(nr)		(((nr) >> _IOC_SIZESHIFT) & _IOC_SIZEMASK)
   
   /* ...and for the drivers/sound files... */
   
   #define IOC_IN		(_IOC_WRITE << _IOC_DIRSHIFT)
   #define IOC_OUT		(_IOC_READ << _IOC_DIRSHIFT)
   #define IOC_INOUT	((_IOC_WRITE|_IOC_READ) << _IOC_DIRSHIFT)
   #define IOCSIZE_MASK	(_IOC_SIZEMASK << _IOC_SIZESHIFT)
   #define IOCSIZE_SHIFT	(_IOC_SIZESHIFT)
   
   #endif /* _UAPI_ASM_GENERIC_IOCTL_H */
    
   ```

   

2. 来看一个例子

   ```c
   /* ioctl(struct file *file, unsigned int cmd, unsigned long arg) */
   /* 使用_IOW等来形成ioct函数中的cmd */
   #define BTMMU_DEL _IOW('b', 0x02, struct btmmu_op_arg)
   ...
   ret = ioctl(btmmu_fd, BTMMU_DEL, (void*)&arg);
   ```

3. 问题

   1. _IOR, _IOW等区别何在？

      _IOR, _IOW等形成了ioctl参数中的高两位，代表数据流动方向：用户程序是向驱动传入数据还是读取数据（代码注释中有更详细的介绍）。不过这里的方向作用不大，更多的意义在于参考。

   2. \_IOR, \_IOW等宏中的(_IOC_TYPECHECK(size)什么功能？

      参考如下链接

      https://lwn.net/Articles/48354/

      https://stackoverflow.com/questions/14934214/what-does-sizeofint1-mean

