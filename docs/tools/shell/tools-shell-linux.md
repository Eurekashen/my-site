---
title: Linux
date: 2023-10-02
author: Shuaike Shen
tags: ['Tools','Linux']
categories: 
- Tools
- Linux
---

# Linux&&Shell

## 关于CUDA

- 以下这个命令可以指定在哪一张显卡上面运行任务

  ```bash
  export CUDA_VISIBLE_DEVICES=5
  ```

- 装显卡驱动，后面跟着的数字是版本号

  ```bash
  sudo apt install libnvidia-common-530
  sudo apt-get -y install libnvidia-gl-530
  sudo apt install nvidia-driver-530
  ```

## 关于网络



## 进程管理

- 直接在终端中运行程序如果ssh连接断开的话程序会中断运行，这时候有下面的方法

  - 使用nohup命令

    ```bash
    nohup command &
    ```

    会把程序挂起到后台运行，程序的输出会默认重定向到日志文件中。执行命令之后会返回一个进程号，可以用于之后kill程序。

  - 使用tmux

    ```bash
    tmux new -s name
    tmux ls
    tmux kill-session -t name
    tmux at -t name
    ```

- nvtop和htop命令

- watch命令，可以用来监听nvidia-smi命令查看GPU的使用

- kill杀死程序（可以一次性杀死多个PID）

  ```bash
  kill -9 <PID>
  ```

- ps ux，显示所有的进程