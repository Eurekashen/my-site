---
title: Chapter3 Processes
date: 2023-10-07
author: Shuaike Shen
tags: ['Lecture Notes','Operating System']
categories: 
- Lecture Notes
- Operating System
---

# Process Concept

在教科书中jobs/process这两个术语是可以相互替换的
Process就是一个正在执行的程序

* 进程的结构图：

  <center>
    <img src="https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-11.24.58.png" style="zoom:80%;" />
  </center>

  * 这里的stack和heap和数据结构中的区分
  * text section存储的是程序的代码，data section存储的global的变量；stack中存储function parameters, local vars, return addresses等；heap中存储的是动态分配malloc的内存；
  * 进程还包含program counter
  * 如果本地变量的太多例如一个超长的数组，就会发生<mark>stack overflow</mark>

# Process State

进程的状态，使用`ps`命令查看进程的状态

* new:  The process is being created

* running：Instructions are being executed

* waiting：The process is waiting/blocked for some event to occur

* ready：The process is waiting to be assigned to a processor
* terminated：The process has finished execution

区分waiting和ready，ready是等待CPU；waiting是等待别的事件（e.g:I/O）的发生

* 使用state diagram来表示state transition

  ![](https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-11.34.44.png)

