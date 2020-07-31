---
title: 修改 R 包自带函数
author: oknet
date: '2020-07-27'
slug: how to modify function from R package
categories:
  - learnR
tags:
  - R包
  - 函数
draft: yes
---

修改R包自带函数，并将其回归到变量环境；
其实是从(周运来的文章)[https://www.jianshu.com/p/80244f3bcca7]中看到的，就记录下来了，因为一定会有这个需求；
原文中更改了estimate中的函数，当然我也认为这个自带函数很烦人类，就是不知道如何操作它，这不来了

```
myfilterCommonGenes <- edit(estimate::filterCommonGenes)
function (input.f, output.f, id = c("GeneSymbol", "EntrezID")) 
{
    stopifnot((is.character(input.f) && length(input.f) == 1 && 
        nzchar(input.f)) || (inherits(input.f, "connection") && 
        isOpen(input.f, "r")))
    stopifnot((is.character(output.f) && length(output.f) == 
        1 && nzchar(output.f)))
    id <- match.arg(id)
    input.df <- read.table(input.f, header = TRUE, row.names = 1, 
        sep = "\t", quote = "", stringsAsFactors = FALSE)
    merged.df <- merge(common_genes, input.df, by.x = id, by.y = "row.names")
    rownames(merged.df) <- merged.df$GeneSymbol
    merged.df <- merged.df[, -1:-ncol(common_genes)]
    print(sprintf("Merged dataset includes %d genes (%d mismatched).", 
        nrow(merged.df), nrow(common_genes) - nrow(merged.df)))
    outputGCT(merged.df, output.f)
}
```{r}


我想直接把seurat的某个gene-cell矩阵对象给它，于是就把输入改成：

>
myfilterCommonGenes <- function(input.f, output.f, id = c("GeneSymbol", "EntrezID"))
{
  id <- match.arg(id)
  input.df <- input.f
  merged.df <- merge(common_genes, input.df, by.x = id, by.y = "row.names")
  rownames(merged.df) <- merged.df$GeneSymbol
  merged.df <- merged.df[, -1:-ncol(common_genes)]
  print(sprintf("Merged dataset includes %d genes (%d mismatched).",
                nrow(merged.df), nrow(common_genes) - nrow(merged.df)))
  outputGCT(merged.df, output.f)
}


为了让改造后的函数依然在estimated的环境之中：

>
environment(myfilterCommonGenes) <-  environment(estimate::filterCommonGenes)
> 

依葫芦画瓢即可，完成，使用的时候记住你的函数名就行；
这里修改一个自己用到的函数，修改fgsea的参数适应新的参数形式；


>
myimmu.LncRNA <- edit(ImmuLncRNA::immu.LncRNA)
>

这里不展示原始函数了，修改位置在24行，两处，修改fgsea新版参数*eps*,另外给循环添加*NA*的判断出口,关于fgsea参看[新版教程](https://bioconductor.org/packages/release/bioc/vignettes/fgsea/inst/doc/fgsea-tutorial.html)


>
#省略了上下文
        fgseaRes <- fgsea(pathways, ranks, minSize = 15, maxSize = 5000，eps=)
        if(length(fgseaRes$pathway)=="NA") {
        (next)()
        }
#省略了上下文
>


>
environment(myimmu.LncRNA) <-  environment(ImmuLncRNA::immu.LncRNA)
>