---
title: Python 正则替换 Markdown 图像路径前缀
date: 2020-05-22
tags:
- Python
categories:
- Coding
- ELSE
---

## 问题描述

在博客网站下直接写文章时，会涉及到图像的路径，一般采用的相对路径。但是这样子在最后部署的时候的路径一般都会出现问题（或者自己另外写好的文章放到网站上就不可避免的重新编辑图像地址）。

一种解决方法是把所有图片放在图床上，就不存在上面的问题了。但本人一般喜欢本地写，图片资源直接放在GitHub上，或者博客网站的相应位置，所以将本地路径转换为 GitHub /网站 路径就显的很重要。

这里采用的是 Python 正则替换实现此功能

<!-- more -->

## 解决方案

这里采用的是Python的正则匹配方案。

Markdown 中的图像都是以 `![text](./img_directory/sub_path)` 或者 `![text](img_directory/sub_path)` 这样的形式，而目标则色把他们的公共前缀路径替换为超链接前缀，如 `![text](https://site/sub_path)`，所以简单的思路就是把图像中的路径 `****prefix/img.jpg` 替换为 `site_path/img.jpg` 即把 prefix 及其前面的部分替换为网址前缀，防止图像路径有的使用 `./` 有的不适用的格式。

代码如下：

```python
# -*- coding: utf-8 -*-
# @Author : Weijun Lin
# @File : change-img-path.py
# @Software: VS Code
# @Version: python 3.7.3
# @Desc: 使用方式 python change-img-path.py tar-markdown-file prefix newprefix

import re
import sys

if len(sys.argv) != 4:
    print("amount of parameters must be 4")
    sys.exit()

all_prefix = sys.argv[2]
patter1 = '\!\[.*?\]\((?P<tar>.*{}).*?.*?\)'.format(all_prefix)
patter1 = re.compile(patter1)
patter2 = '\<img src="(?P<tar>.*{}).*?\..*?\>'.format(all_prefix)
patter2 = re.compile(patter2)
file_path, prefix =sys.argv[1], sys.argv[3]

with open(file_path, "r", encoding='UTF-8') as file_in, open("out-"+file_path, "w", encoding='UTF-8') as file_out:
    line = file_in.readline()
    while line:
        # 获取所有匹配的Match对象
        matched1 = patter1.finditer(line)
        matched2 = patter2.finditer(line)
        pos_list = []
        if matched1 != None:
            pos_list += [(x.start("tar"), x.end("tar")) for x in list(matched1)]
        if matched2 != None:
            pos_list += [(x.start("tar"), x.end("tar")) for x in list(matched2)]
        pos_list.sort() # 按开始位置从小到大排序
        for v in pos_list[::-1]: # 逆序遍历
            st, ed = v
            # 替换掉原来的
            if st == ed:
                line = line[:st] + prefix + line[st:]
            else:
                line = line[:st] + prefix + line[ed:]
        file_out.write(line);
        line = file_in.readline()
    
```

使用方式为 `python change-img-path.py tar-markdown-file prefix newprefix`

使用 `newprefix` 替换图像路径中的 `prefix`

其中一些函数的解释可以参考：[正则表达式操作](https://docs.python.org/zh-cn/3/library/re.html)