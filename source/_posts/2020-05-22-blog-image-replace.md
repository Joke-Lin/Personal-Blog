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

在博客网站下直接写文章时,会涉及到图像的路径，但在本地下的路径和最终部署后的路径是不同。对于不是很长的文章，便直接写路径为`site.com/assert/.../`这样的了。但对于图片过多的情况，这样子渲染起来一堆无效图片，很难受。所以长篇文章就在站点文件夹之外新建一个文件夹写，将图片也存一份到此文件夹下，最后发布的时候，在将所有的图片的路径改成可以显示的。但手动改很麻烦，可以使用正则匹配解决。

<!-- more -->

## 解决方案

这里采用的是Python的正则匹配方案。对于路径`![text](./img/sub_path)`，转换为网站可识别路径只需要将`./`替换为对应的前缀即可。

代码如下:

```python
    # python 3.7.3
    # 使用：python xx.py 输入文件 需要添加的前缀

    import re
    import sys

    if len(sys.argv) != 3:
        print("amount of parameters must be 3")
        sys.exit()

    # 考虑./img/path  img/path 两种种情况
    patter = '\!\[.*?\]\((?P<tar>(\./)?).*?\)'
    patter = re.compile((patter))
    file_path, prefix =sys.argv[1], sys.argv[2]

    with open(file_path, "r", encoding='UTF-8') as file_in, open("out-"+file_path, "w", encoding='UTF-8') as file_out:
        line = file_in.readline()
        while line:
            matched = patter.finditer(line) # 获取所有匹配的Match对象
            if matched != None:
                matched = list(matched)
                for v in matched[::-1]: # 逆序遍历
                    st, ed = v.start("tar"), v.end("tar")
                    # 替换掉原来的
                    if st == ed:
                        line = line[:st] + prefix + line[st:]
                    else:
                        line = line[:st] + prefix + line[ed:]
            file_out.write(line);
            line = file_in.readline()
    
```

> 2020-05-24 更新

上述代码并未考虑到markdown文件中使用`<img src="" ... >`这样的HTML标签的情况：

更新代码如下：

```python
# python 3.7.3
# 使用：python xx.py 输入文件 需要添加的前缀(前缀为 XXXX/)

import re
import sys

if len(sys.argv) != 3:
    print("amount of parameters must be 3")
    sys.exit()

# 考虑./img/path  img/path 两种种情况 以及 使用markdown 和 HTML img标签
patter1 = '\!\[.*?\]\((?P<tar>(\./)?).*?\)'
patter1 = re.compile(patter1)
patter2 = '\<img src="(?P<tar>(\./)?).*?\>'
patter2 = re.compile(patter2)
file_path, prefix =sys.argv[1], sys.argv[2]

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

> 2020-07-20 更新

前面使用与`./img/file_path,img/file_path`的情况

现在给出一种更通用的方式，这里把图片路径看作:

`原始图片路径的公共前缀 + 图片路径`，现在通过正则将原来的前缀包括原始图片公共前缀一起替换掉，如：

`img/c_2/img1.png`，`img/c_3/img2.png`用`https://test/`替换，图片公共前缀为`img/`所以替换后为``https://test/c_2/img1.png`

现在的代码如下：

```python
# python 3.7.3

import re
import sys

if len(sys.argv) != 3:
    print("amount of parameters must be 3")
    sys.exit()

all_prefix = "img/" # 图片公共前缀
patter1 = '\!\[.*?\]\((?P<tar>.*{}).*?.*?\)'.format(all_prefix)
patter1 = re.compile(patter1)
patter2 = '\<img src="(?P<tar>.*{}).*?\..*?\>'.format(all_prefix)
patter2 = re.compile(patter2)
file_path, prefix =sys.argv[1], sys.argv[2]
print(prefix)
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

其中一些函数的解释可以参考：[正则表达式操作](https://docs.python.org/zh-cn/3/library/re.html)