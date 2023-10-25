---
title: Conda
date: 2023-10-02
author: Shuaike Shen
tags: ['Tools','Conda']
categories: 
- Tools
- Conda
---

# Conda

- 从已有的conda环境clone出一个新的conda环境

  ```bash
  conda create --name new_env --clone source_env
  ```

- environment.yaml的使用，创建环境和导出现有的环境

  ```bash
  conda env create -f environment.yaml
  conda env export > environment.yaml
  ```