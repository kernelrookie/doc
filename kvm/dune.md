# dune

## kern 代码结构
dune 是一个"简化版"的 kvm，利用 vt-x 的工作流程是:

1. 进入 guest mode
2. 运行直到遇到在 guest mode 不能安全处理的"事情"
3. 退出到 host 处理, 重新进入

在 kvm 中间，不能安全处理的"事情"主要是特权级指令，而在 dune 中间处理的是 hypercall。

## 地址空间
**dune mode** 指的是被 dune 创建的 guest mode.

在***程序启动***之后，通过 `dune_init_and_enter` 就可以进入到 dune mode.
其中需要建立 vt-x 的 ept page table ，注意，此时程序已经启动了，
需要让 process 无感知的从 HVA 变为 GVA,
HVA 到 HPA 的映射已经存在，所以**需要添加一个线性映射 GVA -> GPA**.


因为 ept pte 的格式和一般的pte格式不同，所以原来的 HVA -> HPA 的 page table 无法直接被
ept 使用。ept 的建立是每次发生 ept violation 的时候，通过 gup 查询
HVA -> HPA, 然后重建。

## syscall
通过 ept 获取的额外的地址空间映射之后，就可以开始随意操作 `GVA -> GPA` 了。

通过 `rdmsrl(MSR_LSTAR, r);` 获取 syscall 的入口地址, 注意这个 HVA，当进入到 Guest mode 之后，
被这个地址是 GVA。想要修改入口地址，只能修改 `GVA -> GPA`。

```
     linear map                    程序启动的时候构建的
GVA ---------------> original GPA ----------------------> original SYSCALL entry
    |
    |
    \   手动设置                 mmap 新的 page 构建的
     \ ------------> new GPA ---------------------------> new SYSCALL entry
```
此时，只要 process 想要调用 syscall, 就会通过 hypercall 也就是 vmcall 的方式通知 host 模拟对应的 syscall (是不是很像 wine).

fork exit 的 syscall 需要特殊的处理，exit 需要将 dune mode 也退出，fork 需要 vmcall 的时候参数。

## 限制
- vt-x
- KASLR : 重写 fork 之类的函数需要调用内核里面的 fork，但是实际上，内核是不提供 fork 函数的调用的，dune 通过分析 /boot/System.map 强行获取
- pthread :

## TODO
- [ ] 理解 vmexit 的原因存在那些类似，是否存在由于 interrupt , divide error 之类导致退出
- [ ] 理解 signal 在 dune 的作用
- [ ] 理解两个 dune 的交互
- [ ] 理解 vsyscall

## 参考资料
- 快速理解 vt-x 机制: https://github.com/LordNoteworthy/cpu-internals
- 论文地址: https://www.usenix.org/system/files/conference/osdi12/osdi12-final-117.pdf
- 代码: https://github.com/project-dune/dune
