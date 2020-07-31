---
title: 重建博客并恢复
author: oknet
date: '2020-06-05'
categories:
  - 折腾
tags:
  - Blogdown
  - Rstudio
  - Git
slug: how to mangage your content via blog and github
draft: yes
---
记录恢复博客的过程；博客假设在Github上存放于docs文件夹内，同时使用 *Rstudio+Blogdown* 书写

content文件夹分类；content、drafts，形如如下的结构；
>
```{r}
|-- about.md  
|-- archives.md  
|-- drafts  
|   |-- CANME  
|   |-- config.tomlbak  
|   |-- how\ to\ mangage\ your\ content.md  
|   |-- how\ to\ setup\ soft-router\ on\ your\ openWRT.md  
|-- posts  
|   |-- learnR  
|   |-- life  
|   |-- play  
|-- tree.txt  
5 directories, 6 files  
```
这是一个文件备份，因为文章只能出现在posts的子文件夹内，所以将文章按照类别分别放在了post下的各个子文件夹内，一是用来分类，二是直接备份文章的markdown原始文件，非常有用；

让文章处于草稿状态且保持未发布状态时，文件夹里drafts文件夹和posts文件平行的位置，然后只要让文章内暂时不加标签即可，不然，加上标签之后就算首页没有看见，但仍旧会在tags页面显示，所以只能用这种方法隐藏未完成的草稿；

顺便记录一下删除重建博客的过程：
> 新建git项目--删除文件夹内所有内容--新建blogdown项目--配置好博客以及主题--下载备份的content及其内容--放回到新建的博客位置即可--重新上传即可完成

新建*.gitignore*文件并输入以下内容
>/*  
!content  
!docs  

```{r}
git rm -rf --cached .
git add .
git commit -m "rebuild"
git push
```

补充 *Centos7* 下 *tree* 的使用
>yum install -y tree  
  tree  
  tree --charset=gbk  
  tree --charset=gbk -L 2  
  tree --charset=gbk -L 2  > tree.txt  
  #tree 文件夹位置 --charset=gbk -L 2  > tree.txt  
  
可能用得到的代码
```{r}
#新建博客
blogdown::new_site(
  dir = "xxx",
  install_hugo = TRUE,
  format = "toml",
  sample = TRUE,
  theme = "yihui/hugo-lithium",
  hostname = "github.com",
  theme_example = TRUE,
  empty_dirs = FALSE,
  to_yaml = TRUE,
  serve = interactive()
)
#安装主题
??install_theme
blogdown::install_theme(
  theme="yihui/hugo-lithium",
  hostname = "github.com",
  theme_example = FALSE,
  update_config = TRUE,
  force = FALSE,
  update_hugo = TRUE
)
blogdown::install_theme("kimcc/hugo-theme-noteworthy", force = TRUE)
#一些风格简单的主题
rhazdon/hugo-theme-hello-friend-ng
cntrump/hugo-notepadium
kimcc/hugo-theme-noteworthy
vividvilla/ezhil
yihui/hugo-lithium
options(blogdown.subdir = 'play')
```
#### Reference
[R Blogdown Setup in GitHub (2)](https://aurora-mareviv.github.io/talesofr/2018/02/r-blogdown-setup-in-github-2/)  