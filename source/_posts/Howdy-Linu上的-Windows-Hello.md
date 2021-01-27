---
title: Howdy Linu上的"Windows Hello"
date: 2021-01-26 23:39:55
tags: [Howdy, Linux]
---
之前电脑手机的密码都是简单的数字密码，这样太不安全了，于是换成了大小写字母、数字、特殊字符组成的混合密码，但是每次解锁屏幕或者执行需要root权限的命令时输入复杂密码又觉得太过于麻烦，于是想到了Windows Hello,小新PRO13是有红外模块的,可是使用Window Hello,但是我几乎不使用Windows,Linux上该如何解决呢？🔍搜索一番发现了一款叫做Howdy,可以使用人脸进行身份验证。
## 安装
```
# yay -S howdy
``` 
## 测试
### 
## 配置
### PAM配置
将下面的信息加入到所有需要人脸解锁PAM配置文件中，`sudo`和`GDM`的屏幕解锁我用的比较多所以在`/etc/pam.d/sudo`和`/etc/pam.d/system-local-login`配置了howdy认证
```
auth sufficient pam_python.so /lib/security/howdy/pam.py
```