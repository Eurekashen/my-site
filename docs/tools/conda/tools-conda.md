---
title: Conda
date: 2023-10-02
author: Shuaike Shen
tags: ['Tools','Conda']
categories: 
- Tools
- Conda
---

## Manage the environment

- Clone a new conda environment from an exisitng environment, and the "source_env" can by a environment name or the path to the environment .

  ```bash
  conda create --name new_env --clone source_env
  ```

- About how to utilize the "environment.yaml". "environment.yaml" is applicable to transfer the conda environment between different operating system.

  ```bash
  conda env create -f environment.yaml
  conda env export > environment.yaml
  ```

!!! tip
  We can put the conda environment the mounted NAS so that we can easily share the conda environment between different server. 
  We just need to modify the initialize path of conda in the `~/.bashrc` file.