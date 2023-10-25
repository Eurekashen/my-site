---
title: Chapter1 Introduction
date: 2023-10-02
author: Shuaike Shen
tags: ['Lecture Notes','Operating System']
categories: 
- Lecture Notes
- Operating System
---

# What is an Operating System?

> A program that acts as an intermediary between a user of a computer and the computer hardware.

是一个在用户和计算机硬件之间的程序，不过现在这句话的定义不是非常的恰当，现在基本都是"many programs"。

* 存在的目的和意义：
  * 执行用户的程序，让解决问题更加的简单、让计算机系统更加的易用。
  * 调度系统的硬件，让硬件高效的运行——在完成相同任务的前提下，占用的资源越少越好；

## Computer System Structure

![](https://z1.ax1x.com/2023/10/02/pPL643Q.png)

事实上我们平时使用的“OS”不是真正的那个“A program”，而是 运行在操作系统上的各种程序。

# OS Definition

* OS is a <mark>**resource allocator**</mark>
  * *Manages all resources；*管理所有的硬件资源-lab1会接触到定时器timer
  * *Decides between conflicting requests for efficient and fair resource use；*当资源出现冲突的是时候决定怎么样的分配是最高效的、最公平的。

* OS is a <mark>**control program**</mark>
  * *Controls execution of programs to prevent errors and improper use of the computer*；当程序出现错误的时候handle这些错误，典型的问题就是C语言对0地址访问之后程序报错退出，不会把整个系统弄崩掉。

## kernel

<mark>**内核**</mark>：操作系统中一直在运行的、最早启动、最先被赋予进程ID的程序就是kernel

> Everything else is either a system program (ships with the operating system) or an application program

从宏观的角度来看，*因为不可能有一个程序始终都占用CPU在运行*

## Computer Startup

* 启动引导程序**bootstrap program**：
  * 当系统加电启动的时候用来把操作系统的内核加载到内存中去并且开始执行
  * 一般来说放到主板上面的ROM&EPROM中，这就是所谓的固件firmware，是烧录在ROM的
  * *对于现代处理器来说，很重要的是建立虚地址*

# Computer System Organization

![](https://z1.ax1x.com/2023/10/02/pPL6qEV.png)

冯诺依曼架构是围绕**内存**展开的，所有设备通过bus和内存建立链接

> I/O devices and the CPU can execute concurrently.

CPU和IO设备是可以并发的运行的

一个device会被对应的device controller管理，有自己Local buffer，CPU控制local buffer和main memory之间的数据交互。I/O is from the device to local buffer of controller.I/O的pipeline是从device到local buffer到controller的。当操作设备的操作完成之后，device会触发<mark>**interrupt中断**</mark>的方式通知CPU.
当设备非常多的时候，有<u>*中断控制器*</u>来管理、控制中断。

# Interrupt

* polling和vectored是两种中断的类型，polling是CPU定期的检查中断是否发生；vectored是使用**中断向量**的方式处理中断。

* 中断向量interrupt vector：向量的每个元素就是对应中断事件的对应处理的首地址。处理中断的时候根据interrupt number来决定要使用哪一个来处理。

* context：上下文，中断处理的关键就是不能丢失上下文

中断只不可预知的，中断的优先级（中断处理的时候又有中断）

trap：软中断，软件触发的中断——程序出错了、故意触发的（user request/system call，是开发人员有意调用操作系统的接口从user code进入system code kernel里面运行，调用系统的服务）。system call的number代表不同的功能

在***RISC-V***中不同的一套术语体系：所有的中断称为traps，下面分为interrupt硬件中断和exceptions异常&ecalls环境调用(RISC-V 中的system call)

## Interrupt handling

保留运行时的context上下文——也就是寄存器的状态和PC(program counter)

## I/O-Two methods

很多的I/O是通过中断的处理来完成的，有同步和异步两种处理中断的方式：

* Synchronous：同步的中断处理，当中断发生之后系统直到中断处理完成之后才恢复正常运行。
* Asynchronous（异步、非阻塞IO）屏幕打印被键盘中断的例子
  * 有两条控制流，一个做I/O处理、一个回到原来的位置继续运行——需要多个线程的参与。
  * 系统不等中断处理完成就恢复运行。
  * 中断处理和系统运行是同时执行的。
  
  ![Screenshot-2023-10-05-at-22.14.47.png](https://s1.imagehub.cc/images/2023/10/05/Screenshot-2023-10-05-at-22.14.47.png)

## Device-Status Table

使用一个链表来管理设备、监控设备的状态、<mark>根据优先级管理</mark>request的队列

![](https://s1.imagehub.cc/images/2023/10/05/Screenshot-2023-10-05-at-22.15.31.png)

## Direct Memory Access Structure

对于高速IO设备中断的效率不够高，可以使用直接内存访问的模式。

DMA能够在CPU不干涉（不是不干预，而是少干预，从per byte—>per block）的情况下和main memory进行数据交互，等到一个block数据传输完了之后才有一个interrupt

# Storage Hierarchy

存储设别在<mark>speed、cost、valatility</mark>三个层面是层级组织的，这里不再赘述。

## Caching

Caching是一种操作，用上层快的缓存下面慢的，<u>main memory可以看作是最后一层cache</u>，用来处理速度不匹配的问题

# Multiprocessor Systems

多处理器系统

* SMP architecture：对称多处理器系统，每个CPU都有自己的寄存器，所有的CPU通过bus共享main memory。
* 多核系统：一个CPU上有多个处理核心，每个core拥有多极缓存多级缓存，在一个chip上的信息传输比chip之间的要快。
* NUMA架构：不是共享内存，每个CPU有属于自己的local memory，别的CPU的memory称为remote memory，都可以访问但是访问remote memory的时候速度会慢。

# Operating System Structure

* **批处理系统, Batch Processing Systems**。Jobs 在内存或者外存里，内存始终有一个 job 在运行，操作系统负责在结束后加载下一个开始运行（我们将加载到内存并运行的程序为 **进程, process**）。
  批处理系统的逻辑简单，弊端也非常明显：当进程执行 I/O 时，CPU 会停下来等待事件完成。I/O 事件有可能是和显示器等设备交互，但更耗时的实际上是等待用户进行输入。在这样的系统里，大多数时间浪费在了等用户输入这件事情上。
* Multiprogramming——更高的CPU利用率：把一堆程序放到内存中，<u>**因为每个user不可能时时刻刻都把CPU占满，所以Multiprogramming这种策略会让调度所有的任务让CPU永远有任务**</u>。任务相关的所有东西都会被存放到内存中，当一个任务有需要等待的东西（I/O或者其他）就会切换到下一个任务。
* Timesharing分时(multitasking)——更高的交互性（命令可以更快的响应）：同时的运行多个任务，每个程序都可以快速的响应。*CPU一会执行这个一会执行那个*，操作系统通过调度让每个user的程序都可以快速的得到响应，**如果main memory的空间不够了要和把这些程序拿进拿出（swapping技术）。还有虚拟内存技术可以允许进程不完全在memory中（把碎片的内存映射成连续的内存）。**

这两种技术是不冲突、不矛盾的，multitasking系统一定是一个Multiprogramming的。

<u>对于多用户的OS来说multitasking就是把时间分成了一个个小的时间片，每一个时间片给特定的一个user；而在这个时间片内对于这个特定的用户OS又是Multiprogramming的。</u>

# Operating-System Operations

操作系统在模式上的分割：User mode和kernel mode(supervisor mode)，两个模式的区别和切换是由**<u>硬件中的一个bit状态位</u>**决定的。有些硬件是privileged，这种硬件只能够在kernel node下面运行，在kernel mode中可以运行“特权”指令（system call，例如在屏幕上显示、调用I/O等），特权指令在user mode中不可以运行。
<mark>隔离：mode是隔离的，在运行kernel code的时候绝对不可能同时运行着user code</mark>

> System call changes mode to kernel, return from call resets it to user
> System call会把模式切换成kernel mode，执行完成之后又会返回到user mode

除了这些还有machine mode，machine mode不是每个系统都有

* system call调用过程

![](https://s1.imagehub.cc/images/2023/10/06/Screenshot-2023-10-06-at-19.07.18.png)

> 其实操作系统就是提供了很多system call让用户可以去调用，这些system call都是可重入的、并发访问的

# Operating-System functions

操作系统的主要功能

## Process Management

> A process is a program in execution
> 进程就是运行当中的程序

* 进程和程序的区别：

* 进程是对resources的一种抽象：

  * 程序是一个passive entity，进程是一个active entity；一个程序可以有多个对应的进程（例如开上多个vim）
  * 进程需要资源来完成任务
  * 进程的终止就是把占用的资源释放
  * 进程的状态：进程的状态由进程对应的<mark>program counter（和组成一样，指示下一条指令的位置）</mark>和例如寄存器的值来描述；对于多线程的进程，每个线程thread会对应一个PC。

* CPU的复用：<mark>multiplexing the CPUs</mark>，只有一个/多个CPU，很多进程共享CPU这一个计算资源并发的运行。时分复用——在时间上分割

  > multiplexing：是一种比较抽象的概念，让多个进程使用有限的资源，有不同的实现策略。

* **进程的管理：建立、删除、恢复、同步、进程之间的沟通、防止deadlock**

## Memory Management

内存管理需哟啊解决的是在空间上分割的复用——空分复用

所有的指令和数据要在进程运行之前加载到内存中（不绝对），管理内存的分配、追踪内存、管理进程使用的内存。

## Storage Management

管理文件和文件系统，文件系统有基本的创建删除文件、目录的功能；还要有海量文件管理的功能，有并发访问控制等功能。

## I/O

* hide peculiarities：IO设备的种类厂家非常多，需要隐藏IO设备的奇异性，让设备用起来简单，对user提供一个比较统一的接口。
* OS要保证IO设备的高效
* OS要提供统一的设备驱动接口、厂家编写对于指定的设备的驱动程序。

