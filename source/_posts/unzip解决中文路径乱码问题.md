---
title: unzip解决中文路径乱码问题
date: 2020-03-16 11:46:41
updated: 2020-03-16 11:46:41
tags: [unzip,Linux,Mojibake]
categories:
- [Linux,unzip]
---
unzip解压从Windos拷贝过来的文件时经常出现乱码，这是因为Linux上unzip默认UTF-8编码，而Windows简体中文版用zip软件压缩时文件名一般会使用GBK编码，这就导致了乱码。  
知道问题了那Google搜一搜怎么解决吧，Google告诉我用-O选项，然而unzip并没有-O选项啊，那哪来的-O呢？原来大神给unzip打了转码补丁[unzip-iconv](https://github.com/m13253/unzip-iconv)，但是unzip自打2009年以来一直没有更新过，iconv也就一直没有进入mainline,所以我们需要用unzip的包含iconv补丁的版本,aur已经有人打了包[unzip-iconv](https://aur.archlinux.org/packages/unzip-iconv/),所以直接安装就可以,aur上的这个版本不仅包含了转码补丁，还包含一些其漏洞的补丁。
```  
# yay -S unzip-iconv
```
cn仓库上也有这个包  
```
# pacman -S unzip-iconv
``` 
这个时候我们就可以使用-O选项了 

```
$ unzip -O [iconv] file[.zip] [-d exdir]
```
e.h.: `unzip -O GBK file.zip`
