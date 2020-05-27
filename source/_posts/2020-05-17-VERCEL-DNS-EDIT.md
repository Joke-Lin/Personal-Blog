---
title: VERCEL（原名为ZEIT）DNS 记录的修改
date: 2020-05-17
categories:
- 随笔
- Tutorial
---

> VERCEL提供静态网站的部署和CDN的加速，十分优秀的工具（适合白嫖）

## 问题简述

在部署自己的静态网站后（从GITHUB），VERCEL提供了他们的子域名，当然可以使用自己的域名。可以用CNAME将域名导向VERCEL的子域名，也可以直接使用我们自己的域名（不通过CNAME）。具体配置参考：[Custom Domain](https://vercel.com/docs/v2/custom-domains)

但直接使用自己的域名会出现一个问题，使用VERCEL提供的DNS服务器后，原来配置的DNS解析失效了。原来我使用的是阿里云的云解析，在更改为VERCEL的DNS服务后，子域名的A记录失效。

<!-- more -->

## 解决方案

既然域名解析失效，因为DNS服务器改成了VERCEL的，所以我们想要改回来需要通过VERCEL修改DNS记录。这需要通过VERCEL的CLI工具实现。下载地址为：https://vercel.com/download

需要先登录，执行：`vercel login`，登录好之后，就可以修改DNS解析了。（这里我登录验证了好久……）

跟着vercel的官方教程即可：https://vercel.com/docs/cli#commands/domains

`vercel dns add [domain] [subdomain] [A || AAAA || ALIAS || CNAME || TXT] [value]`

便可以修改记录