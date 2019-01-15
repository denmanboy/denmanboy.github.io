---
title: KVC中对对象的属性赋值的顺序
toc: true
date: 2014-04-18 18:41:31
tags:
      - iOS
      - KVC
categories: technology
description: KVC编程中对对象属性赋值的顺序
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
在KVC编程当中找对象的属性的顺序如下  
例如 [object    setValue:@"邓小平" forKey:@"name" ];   
那么KVC找属性的顺序如何呢

     1.找_name的成员变量
     2.找叫name的属性
     3.找叫name的成员变量
     4.调用setValue:forUndefinedKey:];

所以一个对象用到KVC对属相赋值的时候 一定要实现<br> setValue:<#(id)#> forUndefinedKey:<#(NSString *)#>;方法
不然当 

     1.找_name的成员变量
     2.找叫name的属性
     3.找叫name的成员变量
都找不到的时候 会报错！
