# OS_linux

bootset.s

操作系统在硬盘上

计算机的工作是取指执行

第一步就是从磁盘上把操作系统读到内存里

引导工作就是blos























































刚开机时，内存里面只有这个地方有数据，这部分程序的功能是：检查RAM、键盘、显示器等硬件，这个地方就是ROM BLOS映射区（只读）

读0x7c00

引导程序bootsect.s汇编程序

```cpp
BOOTSEG = 0x07c0
INITSEG = 0x9000
SETUPSEG = 0x9020

entry start //关键字entry告诉链接器“程序入口”
start:
    mov ax, #BOOTSEG mov ds, ax
    mov ax, #INITSEG mov es, ax
    mov cx, #256
    sub si, si sub di,di
    rep movw
    jmpi go, INITSEG

go: mov ax,cs //cs=0x9000
    mov ds,ax mov es,ax mov ss,ax mov sp,#0xff00
load_setup: //载入setup模块
    mov dx,#0x0000 mov cx,#0x0002 mov bx,#0x0200
    mov ax,#0x0200+SETUPLEN int 0x13 //BIOS中断
    jnc ok_load_setup
    mov dx,#0x0000
    mov ax,#0x0000 //复位
    int 0x13
    j load_setup //重读
```

操作系统的接口
什么是操作系统的接口
接口其实是一种抽象，比如插排，它将内部的电路全部封装起来，只提供两个插口，用电设备插上就能用；不用管插座内部是如何实现的。操作系统的接口也是如此，操作系统的接口其实就是一个个函数，知道它的功能然后直接调用就行，而不用管它内核里面是怎么实现的，因为这个函数是系统调用的，所以也称为系统调用。比如：write()、read()等等

POSIX标准 可移植性

1. 用户程序能不能直接调用系统内核

   >  不能

用户态访问内核态只能通过一种途径，那就是中断

