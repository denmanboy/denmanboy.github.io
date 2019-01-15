---
title: '?:和Swift中??异曲同工'
toc: true
tags: 
       - Swift
       -  \?:
categories: technology
date: 2015-01-12 17:08:57
description:  ?:(条件表达式) 条件表达式的另类用法，和Swift中的??(空合并运算符)
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
## ?? 

可选类型数值如果为空是，不能参与运算的，所以在使用之前要对它进行判断非空操。空合并运算符用于对可选类型进行空判断，如果包含值就对可选常量/变量进行解包，如果为nil则返回式默认值。空合并运算符使用默认值替换nil值，处nil值的情况。保证了表达式一定有值，提高程序的健壮性。

	a ?? b
	
它是对一下表达式的简单表达方式

	a != nil ? a! : b
	
当可选类型a 不为空时，对a进行强制解包，取出a的值。反之，当a为空时，返回默认的b值。其中b可以是字面量，常量，变量，或者表达式，都可以。


？？ 有两个条件

表达式a必须是可选类型

默认值b的数据类型必须和a的值数据类型一致


# ?:
?: 是条件表达式式 ，是唯一的一个三元运算符，其实他是对下面表达式的简写

    int a = 2;
    int b = 3;
    int c;
    //条件表达式
    //c = a > b ? a : b ;
    //if-eles 条件判断
    if (a > b) {
        c = a;
    }else{
        c = b;
    }
  
一般我们常用?: 简写if-else判断，增进写代码的速度，其实?: 还有一个和Swift中?? 相似的作用，用作二元表达式，用来返回默认值，例如，有时做网络请求时，服务器返回为空，但是我们UI见面不能显示为空，就显示一个默认值。
    
    NSString *UIString = serverString ?: @"defultString";
    //等价于
    NSString *UIString = serverString ? serverString : @"defultString";
    
鉴于以上对?: 做过二元运算符的使用，在使用block时我们每次都要做判断是否为空，如下？

    //先判断是否为空，再调用block
    if (block) {
        block();
    }
    
可用二元表达式简写如下

    !block ?: block()


















