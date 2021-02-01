---
title: uGet调用aria2实现极速下载
date: 2020-03-07 00:00:28
updated: 2020-03-07 00:00:28
tags: [Linux, uGet, Downloader]
categories:
- [Linux]
---
我在Linux上一直使用uGet作为下载器下载文件，但是感觉下载速度不如aria2，于是想用上aria2插件。
*** 
首先安装aria2、uGet  
`sudo pacman -S aria2 uget`  
安装uget-integrator插件  
`yay -S uget-integrator`  
安装浏览器对应的链接工具  
Chrome  
`yay -S uget-integrator-chrome`  
Firefox  
`yay -S uget-integrator-firefox`

浏览器安装下载插件 uGet Integration  
[Chrome](https://chrome.google.com/webstore/detail/uget-integration/efjgjleilhflffpbnkaofpmdnajdpepi)  
[Firefox](https://addons.mozilla.org/en-US/firefox/addon/ugetintegration/)  

uGet Edit-->Settings-->Plug-in 选择 aria2  
Arguments 输入  
`--enable-rpc=true  --rpc-allow-origin-all -D --disable-ipv6 --check-certificate=false`

重启Uget 便可以调用aria2