---
title: Chapter3 Processes
date: 2023-10-07
author: Shuaike Shen
tags: ['Lecture Notes','Operating System']
categories: 
- Lecture Notes
- Operating System
---

## Process Concept

由于某些历史原因，在教科书中jobs/process这两个术语是可以相互替换的
Process就是一个正在执行的程序，也就是说进程是一个程序的实例Entity.

* 进程的结构图：

  <center>
    <img src="https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-11.24.58.png" style="zoom:80%;" />
  </center>

  * 这里的stack和heap和数据结构中的区分
  * text section存储的是程序的代码，data section存储的global的变量；stack中存储function parameters, local vars, return addresses等；heap中存储的是动态分配malloc的内存；
  * 进程还包含program counter
  * 如果本地变量的太多例如一个超长的数组，就会发生<mark>stack overflow</mark>

## Process State

进程的状态，使用`ps`命令查看进程的状态

* new:  The process is being created

* running：Instructions are being executed

* waiting：The process is waiting/blocked for some event to occur

* ready：The process is waiting to be assigned to a processor
* terminated：The process has finished execution

==区分waiting和ready，ready是等待CPU；waiting是等待别的事件（e.g:I/O）的发生==

* 使用state diagram来表示state transition

  ![](https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-11.34.44.png)

## Process Control Block (PCB)

进程控制块，每一个进程对应唯一的一个PCB。

 PCB中会包含进程相关的信息：

- **Process state**：进程状态
- **Program counter** ：进程运行到
- **CPU registers** ，存储所有进程相关的寄存器的值
- **CPU scheduling information** ，properities, counter
- **Memory-management information** 
- **Accounting information** ，CPU 使用时间、时间期限、记账数据等
- **I/O status information** ，分配给进程的 I/O 设备列表、打开文件列表等

