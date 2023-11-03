---
title: Chapter2 Operating-System Structures
date: 2023-10-02
author: Shuaike Shen
tags: ['Lecture Notes','Operating System']
categories: 
- Lecture Notes
- Operating System
---

## Operating System Services

* 从user的角度：

  * User interface：UI，所有的操作系统都需要提供UI用户界面，CLI、GUI

  * Program execution：OS必须能够把程序加载到内存中并且执行、检测错误
  * I/O operations
  * File-system manipulation：需要管理和实现文件系统
  * Communications：在一个计算机内或者多个计算机之间
  * Error detection：硬件和软件的错误

* 从system的角度：
  * **Resource allocation**：分配CPU等计算资源、memory等存储资源
  * Accounting：监视每个user使用了多少资源；
  * Protection and security：并发的多用户进程不应该互相干涉；
    * Protection：保证系统的所有资源都是在OS的控制之下的；
    * security：用户登陆验证、拒绝没有被授权的I/O设备连接；

## System Calls

又称为trap、软中断，是OS提供的调用系统服务的编程接口，一般是使用高级编程器语言诸如C/C++编写的。

* System Call和API：API是更高层级的概念，一般我们是通过调用API而不是直接的调用System Call，这样更加的简便。

* 常见的System Calls：

  > Three most common APIs are Win32 API for Windows, POSIX API for POSIX-based systems (including virtually all versions of UNIX, Linux, and Mac OS X), and Java API for the Java virtual machine (JVM)

### System Call Implementation

* number：首先所有的system call都要有一个number，代表了不同功能的system call；通过统一的接口trap进kernel之后通过这个number判断是哪个system call。一般使用一个table，number就是对应的下标。
* 调用者可以是programmer也可以是standard library，实现细节是被隐藏的。

![](https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-14.28.01.png)

### System Call Parameter Passing

* registers：使用寄存器传递参数
* block/table：
  * 使用main memory中的内存快来传递参数，拿到block的地址/首地址，把首地址存储到寄存器中传给system call
  * 需要注意的问题是main memory实际上是virtual memory的形式组织的，在kernel（system call）下的VM和user code下的VM可能对应的不是同一片内存空间，需要做转换。
* stack：和上面需要注意的问题一样，对应的stack也不是同一个，要做转换。

![](https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-14.31.30.png)
*注意上图没有画出virtual address转换的过程*

### Types of System Calls

System call的种类

* Process control
* File management
* Device management
* Information maintenance (e.g. time, date)
* Communications
* Protection

例子如下表：

![](https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-14.29.20.png)

## System Programs

System Programs提供了一个便捷的开发环境，大多数的user实际上是在使用system program而不是真正的system call；可以被分为以下部分：

* File manipulation：管理文件
* Status information：检测系统的状态
* File modification：读写文件
* Programming language support：编译器、调试器、解释器
* Program loading and execution：可以执行程序
* Communications：提供在processes, users, and computer systems之间的的沟通机制
* Application programs：

## Operating System Design and Implementation

* OS设计和实现的原则：

  * 不是一个解决问题的过程——not “solvable”
  * 从goals和specifications开始
  * 选择硬件平台
  * User goals和system goals

* Important principle to separate，要把两者分开来获得最大的灵活性；

  * Policy:   What will be done?策略（确定具体做什么事）
  * Mechanism:  How to do it?机制（定义做事方式）

  > 一个例子是酒店房卡的例子，房卡能开某个房间的门不是烧录在数据库系统中的，而是可以更新绑定的，如果是固定在数据库代码中的那么如果丢卡了就很尴尬
  >
  > 还有就是系统的配置文件都是policy，在/etc目录下

## Operating System Structure

只是一种概念的分层，在源代码层次没有这样的分层

### Simple Structure 

所有的都是揉到一起的，典型的代表就是MS-DOS系统

### Layered Approach

分层的结构，最底层是硬件、最上层是user

### Monolithic structure

宏内核结构，放在kernel中的东西多，把所有的功能都在内核中实现了

<center>
  <img src="https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-14.59.17.png" style="zoom:80%;" />
</center>



### Microkernel System Structure

微内核，典型的代表是mac os

kernel中只实现基本的必须的功能，其余功能使用user code来实现

user modules之间通过message passing来沟通，效率低（因为要先进入kernel再出来）但是bug少（kernel精简代码少）

![](https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-15.00.15.png)

### Modular kernel

模块化的kernel

<mark>loadable kernel module</mark>

xxx.ko文件就是对应的内核文件

*Linux某种程度上是宏内核，但是采用了一些模块化的方式使得kernel更精简，可以支持的功能也更多。*

### Other Structures

* Exokernel：“外核”

  * 只负责资源分配，提供了低级的硬件操作，必须通过定制library供应用使用
  * 高性能，但定制化library难度大，兼容性差；<u>和微内核相比不需要message passing而是使用library。</u>

  <center>
    <img src="https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-15.03.09.png" style="zoom:50%;" />
  </center>

* Unikernel

  * 所有的东西都放到一个空间内调用，整个操作系统是一个可运行的实体
  * 速度快，启动的时候是把OS和APP一起boot的，Good for cloud service.

  <center>
   <img src="https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-15.04.08.png" style="zoom:50%;" />
  </center>

## Virtual Machines

使用layered approach，最后把硬件都虚拟出来了，让OS kernel认为自己是在硬件上面运行的；物理机把资源共享给了虚拟机。

<center>
  <img src="https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-15.18.08.png" style="zoom:67%;" />
</center>

* <mark>hypervisor</mark>：可以模拟出硬件，Hypervisor的主要作用是管理和分配物理计算机的硬件资源，以便多个虚拟机能够同时运行而不会相互干扰；虚拟机和物理机之间也不会互相干扰；
  * **资源隔离**：Hypervisor确保虚拟机之间的资源完全隔离，因此一个虚拟机的问题不会影响其他虚拟机。
  * **硬件共享**：多个虚拟机可以共享物理计算机的硬件资源，从而更有效地利用硬件。
  * **快速部署和管理**：虚拟机可以很容易地创建、复制和管理，这使得在同一台物理计算机上运行多个操作系统变得非常方便。

* Linux Containers：是安装运行在OS上面的，提供了一个视图可以客制化看到不一样的文件文件系统。相较于VM更加小巧，是一种轻量级的虚拟化技术，同样满足隔离性等特性。

![](https://s1.imagehub.cc/images/2023/10/07/Screenshot-2023-10-07-at-15.15.55.png)