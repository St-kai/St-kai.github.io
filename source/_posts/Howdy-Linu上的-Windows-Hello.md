---
title: Howdy Linu上的"Windows Hello"
date: 2021-01-26 23:39:55
updated: 2021-01-29 23:27:00
tags: [Howdy, Linux]
categories: [Linux, Howdy]
---
我电脑上一直用着简单的数字密码，输入起来比较方便，但是这样太不安全了，于是换成了大小写字母、数字、特殊字符组成的混合密码，但是每次解锁屏幕或者执行需要root权限的命令时输入复杂密码又觉得太过于麻烦，于是想到了Windows Hello,小新PRO13是有红外模块的,可以使用Window Hello,Linux上该如何解决呢？🔍搜索一番发现了一款叫做Howdy,可以使用人脸进行身份验证。
## 安装
```
# yay -S howdy
``` 
## 使用前的准备
#### 获取设备列表
```
$ v4l2-ctl --list-devices

Integrated Camera: Integrated C (usb-0000:03:00.4-2):
  /dev/video0
  /dev/video1
  /dev/video2
  /dev/video3
```
#### 测试摄像头能否正常使用
```
$ ffplay /dev/videox
```
测试之后发现我的只有`/dev/video0`可以使用。在使用Windows Hello的时候会亮一个红灯但是直接测试`/dev/video0`并没有亮，可能没有正常打开红外模块。  
于是我又在Google上搜索了一番，发现了有大佬写过一个启用红外模块的脚本[linux-enable-ir-emitter](https://github.com/EmixamPP/linux-enable-ir-emitter)。于是从GayHub上clone下来之后用Gcc编译
##### 编译`linux-enable-ir-emitter`
```
$ gcc enable-ir-emitter.c -o enable-ir-emitter
```
然后报错
根据`README`的指引修改`enable-ir-emitter.c`，复制`config.txt`的第二组配置继续编译，成功。
##### 运行编译后的`enable-ir-emitter`
```
$ .\enable-ir-emitter
```
这时候用`ffplay`测试，发现`/dev/video2`已经可以正常开启红外发射器了。
#### 开机加载`enable-ir-emitter`脚本
每此开机的时候总不能都手动解锁然后运行脚本吧，这样就失去了此次折腾的意义。  
配置`systemd`服务让它开机自动加载。

##### 复制编译后的文件到`/usr/loacl/bin`
```
# cp enable-ir-emitter /usr/local/bin
```
##### 复制`enable-ir-emitter.service`到`/etc/systemd/system/`
```
# cp enable-ir-emitter.service /etc/systemd/system/
```
##### 启用服务
```
# systemctl enable enable-ir-emitter
```
这样就可以在开机的时候自动加载脚本了
#### 查看摄像头的支持格式
```
$ v4l2-ctl -d DEVICE_PATH --list-formats-ext
ioctl: VIDIOC_ENUM_FMT
  Type: Video Capture

  [0]: 'GREY' (8-bit Greyscale)
    Size: Discrete 640x360
      Interval: Discrete 0.067s (15.000 fps)
      Interval: Discrete 0.033s (30.000 fps)
```
## 配置
### PAM配置
将下面的信息加入到所有需要人脸解锁PAM配置文件中，`sudo`和`GDM`的屏幕解锁我用的比较多所以在`/etc/pam.d/sudo`和`/etc/pam.d/system-local-login`配置了howdy认证
```
auth sufficient pam_python.so /lib/security/howdy/pam.py
```

#### 配置Howdy
```
# howdy config
```
默认使用`nano`编辑器打开，我使用`vim`编辑器，需要在命令前加上`EDITOR`环境变量
```
# EDITOR=vim howdy config
```
##### 配置摄像头路径
```
device_path = /dev/video2
```
##### 设置黑暗阈值
dark_threshold = 90
### 添加人脸模型
```
# howdy add
```
## 测试
```
# sudo -i
```
这时候Howdy已经可以正常调用红外摄像头了
### 其它
使用几次后发现每次调用Howdy的时候终端都会出现警告，根据ArchWiki的提示，这是`opencv`的警告，可以将`opencv`的日志级别降低
```
export OPENCV_LOG_LEVEL=ERROR
```
将上面的代码加入到`.zshrc`(zsh用户)或者`.bashrc`(bash用户)每次调用Howdy认证的时候终端就不会有警告了。

## 参考
- [Howdy](https://github.com/boltgolt/howdy)
- [Howdy - ArchWiki](https://wiki.archlinux.org/index.php/Howdy)
- [linux-enable-ir-emitter](https://github.com/EmixamPP/linux-enable-ir-emitter)