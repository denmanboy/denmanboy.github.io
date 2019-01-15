---
title: 关于Build Active Architecture Only属性
toc: true
tags: iOS
categories: technology
date: 2015-03-06 17:27:21
description: 关于Build Active Architecture Only属性
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


Build Active Architecture Only这个属性，设置为NO时，会编译多个版本，不过打出来的包会大不少。
这个属性设置为yes，是为了debug的时候编译速度更快，它只编译当前设备的architecture版本。
而设置为no时，会编译所有的版本。
这个是正对当前设备对应的architecture：
armv6：iPhone 2G/3G，iPod 1G/2G
armv7：iPhone 3GS/4/4s，iPod 3G/4G，iPad 1G/2G/3G
armv7s：iPhone5, iPod5
编译出的版本是向下兼容的，比如你设置此值为NO，用iphone5编译出来的是armv7s版本的，iphone4也可以运行，但是armv6的设备就也能运行。
所以，一般debug的时候可以选择设置为yes，release的时候要改为no，以适应不同设备。