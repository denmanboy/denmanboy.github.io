---
title: iOS的release和debug有什么区别
toc: true
date: 2013-08-10 14:23:36
tags: iOS
categories: technology
description: 简介release和debug的区别
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

Release是发行版本,比Debug版本有一些优化，文件比Debug文件小 Debug是调试版本，Debug和Release调用两个不同的底层库。
一、"Debug是调试版本，包括的程序信息更多"
二、只有DEBUG版的程序才能设置断点、单步执行、使用TRACE/ASSERT等调试输出语句。
三、REALEASE不包含任何调试信息，所以体积小、运行速度快。

