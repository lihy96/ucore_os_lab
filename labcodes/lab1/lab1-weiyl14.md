﻿# lab1 实验报告

## 练习1：理解通过make生成执行文件的过程。（要求在报告中写出对下述问题的回答）

**操作系统镜像文件ucore.img是如何一步一步生成的？(需要比较详细地解释Makefile中每一条相关命令和命令参数的含义，以及说明命令导致的结果)**

1. 内核代码和bootloader代码被编译、链接；
2. 使用`dd`命令把内核和bootloader写入ucore.img内。

**一个被系统认为是符合规范的硬盘主引导扇区的特征是什么？**

从`bootmain.c`当中的检查来看，特征应为头4个字节为0x7f,'E','L','F'。

## 练习2：使用qemu执行并调试lab1中的软件。（要求在报告中简要写出练习过程）

这里仅列出一些重要的步骤：

* `set architecture i8086`来观察BIOS的执行
* `br *0x7c00`来设置练习所要求的断点
* `layout split`以方便地观察反汇编

## 练习3：请分析bootloader是如何完成从实模式进入保护模式的。

1. 开启A20（出于兼容性考虑，line 20固定为0，bootloader需要取消这一操作）；
2. 调用lgdt初始化GDT，这里bootloader设置了一个使得虚拟地址等于物理地址的GDT；
3. %cr0 |= CR0_PE_ON，进入保护模式。

## 练习4：分析bootloader加载ELF格式的OS的过程。（要求在报告中写出分析）

利用`x86.h`提供的`outb()`和`insl()`函数逐个扇区读取储存在硬盘中的OS，判断其是否符合ELF格式特征，然后跳转到其入口函数处。

## 练习5：实现函数调用堆栈跟踪函数 （需要编程）

这实际上较为简单，跟随%ebp逐个访问栈帧即可。

## 练习6：完善中断初始化和处理 （需要编程）

**中断描述符表（也可简称为保护模式下的中断向量表）中一个表项占多少字节？其中哪几位代表中断处理代码的入口？**

8字节，最高16位和最低16位联合起来表示中断处理代码在指定的代码段里的内存偏移量。

## 扩展练习

由于`make grade`当中的脚本检查了Challenge 1的实现，这里完成了它，但Challenge 2没有实现。

主要的思路是阅读Intel的手册，弄清`iret`执行的流程，在此基础上就能通过恰当的操纵栈的内容，使从中断处理调用`iret`时达到目标ring。详见代码。
