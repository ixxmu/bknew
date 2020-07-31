---
title: 迁移并重新部署R包
author: oknet
date: '2020-06-17'
slug: how to install your R packages quickly
categories:
  - 折腾
tags:
  - R
  - R包
draft: yes
---
备份升级R迁移R包并不靠谱，Rstudio 自带的Jobs工具很不好用，所以就重新安装一遍几乎所有的R包；
```
#设置清华源
options("repos" = c(CRAN="https://mirrors.tuna.tsinghua.edu.cn/CRAN/"))
install.packages("devtools")
library(devtools)
install_github("YuLab-SMU/wget") 
library(wget) 
#wget这个包真好用，特别是网络环境烂的时候
#至此再安装其他包
if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install("GEOquery")
install.packages("remotes")
remotes::install_github("icbi-lab/immunedeconv")
remotes::install_github("jmzeng1314/AnnoProbe")
BiocManager::install("clusterProfiler")
BiocManager::install("SingleR")
#貌似安装过几个大的包就可以安装工大部分包了；
install.packages('Seurat',dependency=TRUE)
# install.packages('Seurat',dependency=TRUE,INSTALL_opts = '--no-lock')
library(seruat) #如果提示安装miniconda，跳过不要安装
install.packages("tidyverse")
BiocManager::install("CancerSubtypes")
```
纯粹记录，顺便贴上编译自带MKL的sessionInfo

```
R version 4.0.1 (2020-06-06)
Platform: x86_64-pc-linux-gnu (64-bit)
Running under: CentOS Linux 7 (Core)

Matrix products: default
BLAS/LAPACK: /opt/intel/compilers_and_libraries_2020.1.217/linux/mkl/lib/intel64_lin/libmkl_gf_lp64.so

locale:
 [1] LC_CTYPE=en_US.UTF-8       LC_NUMERIC=C              
 [3] LC_TIME=en_US.UTF-8        LC_COLLATE=en_US.UTF-8    
 [5] LC_MONETARY=en_US.UTF-8    LC_MESSAGES=en_US.UTF-8   
 [7] LC_PAPER=en_US.UTF-8       LC_NAME=C                 
 [9] LC_ADDRESS=C               LC_TELEPHONE=C            
[11] LC_MEASUREMENT=en_US.UTF-8 LC_IDENTIFICATION=C   
```
