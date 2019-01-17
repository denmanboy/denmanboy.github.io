---
title: iOS逆向砸壳分享
toc: true
tags: iOS
categories: technology
date: 2019-01-17 17:51:44
description: iOS 砸壳ipa分析相关，记录一下
---
<script>
(function(){
    var bp = document.createElement('script');
    var curProtocol = window.location.protocol.split(':')[0];
    if (curProtocol === 'https') {
        bp.src = 'https://zz.bdstatic.com/linksubmit/push.js';        
    }
    else {
        bp.src = 'http://push.zhanzhang.baidu.com/push.js';
    }
    var s = document.getElementsByTagName("script")[0];
    s.parentNode.insertBefore(bp, s);
})();
</script>
<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# 概述
主要介绍iOS端逆向分析流程及相关操作工具使用。

起因：开发者提交到AppStore的App都是经过FairPlay作为版权保护而加密的，经过加密的App无法被Class-Dump，逆向过程中需要先对App脱壳之后才可以进行分析。

原理：在iOS系统中，可执行文件及动态库都是使用DYLD加载执行，在该过程中，系统先进行DRM检查，检查通过则会从App的可执行文件中选择适合该设备架构的Mach-O镜像进行解密，然后载入到内存中执行，这个过程中解密操作已经完成，解密工具并不做解密操作，而是遍历loadcommand中所有的LC_ENCRYPTION_INFO的信息，将对应解密后的数据从内存中dump出来复写到Mach-O文件中生成新的镜像文件。

dumpdecrypted解密原理：http://www.cocoachina.com/ios/20180415/23015.html
# 砸壳所需条件
## 硬件设备
 越狱手机（设备型号为64位机，否则砸壳以后所得二进制文件只支持armv7指令集无法在64位机上安装使用）
## 软件设备
 越狱手机需要安装：OpenSSH / Cycript
# 砸壳工具
## Clutch
  Clutch：下载地址：https://github.com/KJCracks/Clutch/releases
### Clutch 砸壳步骤
 1.越狱手机和Mac需接入同一个无线网，SSH到越狱手机
 2.将下载好的Clutch拷贝到越狱手机的/usr/bin目录下，增加Clutch的可执行权限 chmod a+x Clutch
 3.打开越狱手机上的Terminal：
输入指令:
-->su：获取最高权限；
-->Clutch -i：查看设备上已安装的app
-->Clutch -d + app序号：开始执行砸壳
## Dumpdecrypted
  Dumpdecrypted：下载地址:https://github.com/stefanesser/dumpdecrypted
### Dumpdecrypted步骤
1.下载完dumpdecrypted，进去下载目录执行-->make：编译动态库dumpdecrypted.dylib，该动态库是需要拷贝到设备目录下的，拷贝之前需要对该动态库进行签名执行-->codesign --force --verify --verbose --sign "iPhone Developer: xxx xxxx (xxxxxxxxxx)" dumpdecrypted.dylib
2.SSH连接到越狱手机，开始执行命令
-->su mobile
-->ps -e | grep AppName：（AppName为解压ipa包之后ipa二进制文件名）, 之后会输出该app的可执行文件路径:以抖音为例：/var/containers/Bundle/Application/B87A9490-A41E-4C09-98DB-6E77E6200FF1/Aweme.app/Aweme
3.-->cycript -p AppName:（锁定该app）
-->[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask][0]:（后面会输出Documents路径）/var/mobile/Containers/Data/Application/F5621336-6837-4E0C-A532-C26C654B30F8/Documents/
4.-->scp dumpdecrypted.dylib 到上面获得的Documents下
-->DYLD_INSERT_LIBRARIES=dumpdecrypted.dylib + 砸壳App路径
执行之后的AppName.decrypted就是我们需要的脱壳之后的二进制文件
## Frida 
环境配置：
iOS端：打开cydia添加源：https://build.frida.re,打开刚添加的源安装frida
Mac端：安装python，wget，pip依赖工具，利用pip进行frida安装：sudo pip install frida
配置frida-ios-dump环境：从github下载工程：sudo mkdir /opt/dump && cd /opt/dump && sudo git clone https://github.com/AloneMonkey/frida-ios-dump，安装依赖：
-->sudo pip install -r /opt/dump/frida-ios-dump/requirements.txt --upgrade
开始执行砸壳：
usb连接到设备：Mac端新开终端输入：
-->cd frida-ios-dump目录
-->./dump.py l:查看设备上已安装的应用
-->/dump.py +应用名称：开始砸壳
砸壳成功之后会自动拷贝回到frida-ios-dump目录
# 砸壳检测
查看ipa是否已脱壳成功：otool -l +二进制文件路径| grep crypt
Cryptid 0表示已经脱壳成功
# IPA二进制文件分析
## 分析工具
### class-dump
class-dump:导出头文件，下载地址:http://stevenygard.com/projects/class-dump/
执行命令：class-dump -H + 可执行文件路径 -o + 输入头文件目录
执行完成之后，头文件就会dump出来，我们可以根据头文件的属性方法信息分析文件结构
### IDA
IDA：静态调试工具
将脱壳之后的二进制文件拖入到IDA之后，会解析出[类名+方法名],结合class-dump导出的头文件可以在IDA里面搜索对应的方法，双击方法名，进入右侧汇编窗口，F5之后可以看方法类的伪代码，进行代码分析
### LLDB
#### LLDB调试步骤
1.LLDB:动态调试工具（Xcode自带调试工具）
restore-symbol:符号表恢复工具，下载地址:https://github.com/tobefuturer/restore-symbol
下载完成后进入restore-symbol目录，执行make生成restore-symbol可执行文件

2.符号表导出：
-->restore-symbol + 砸壳之后的二进制文件 -o + 目的文件路径
将生成的恢复符号表之后的二进制文件替换掉砸壳之后ipa包，重新签名之后，安装到越狱手机，可以开始执行动态调试了

3.SSH到越狱手机，将debugserver文件拷贝到Mac上，执行命令：
-->scp root@xx.xx.xx.xx:/Developer/usr/bin/debugserver /Mac路径

4.对debugserver签名授权：为了能调试非Xcode安装的第三方应用，需要给debugserver增加权限，需要准备entitlements.plist文件，文件内容如下：
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
<key>com.apple.springboard.debugapplications</key> <ture/>
<key>run-unsigned-code</key> 
<ture/>
<key>get-task-allow</key> 
<ture/>
<key>task_for_pid-allow</key>
<ture/>
</dict>
</plist>，
创建好entitlements.plist之后，开始对debugserver签名，执行命令：
-->codesign -s - --entitlements entitlements.plist -f debugserver

5将重签之后的debugserver拷贝回到越狱手机，执行命令：
-->scp debugserver root@xx.xx.xx.xx:/usr/bin/debugserver

6.SSH到越狱手机，执行命令：
-->debugserver *:1234 -a AppName
另起一个终端窗口，执行命令:
-->lldb
-->process connect connect://xx.xx.xx.xx:1234
之后对应的应用就被断住，执行命令：
-->c
放开断点，往下继续走

7.给app下断点，参考class-dump导出的头文件，给对应的方法下断点
执行命令：
-->breakpoint set -n xxx：方法名：如果app中对应的点击事件和猜测的方法名匹配，断点就会断住
删除断点，执行命令：
-->breakpoint delete xxx：断点id
-->bt：打印堆栈信息


根据上面动态调试信息，我们就可以根据具体需求，编写动态库进行Hook操作了，编写完动态库之后，就可以进行代码注入重新签名操作

 









