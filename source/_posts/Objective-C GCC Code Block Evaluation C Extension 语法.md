---
layout: objective-c
title: Objective-C GCC Code Block Evaluation C Extension 
date: 2014-05-20 14:03:40
tags: Object-c
description:  GCC编译器的提供的一个语法
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

# 示例
<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>
先看这样一些代码
<pre>
<code>
    self.nameLabel = ({
        UILabel *label = [[UILabel alloc] initWithFrame:CGRectMake(10, 10, 100, 30)];
        label.backgroundColor = [UIColor yellowColor];
        label.text = @"name";
        label.font = [UIFont systemFontOfSize:15];
        label.textColor = [UIColor yellowColor];
        label;
    });
</code>
</pre>
# 解读
这个是GCC编译器的提供的一个语法
可以看到把一个控件的所有属性全部写在({...})中，这样两个好处一个是结构会更加清晰，封装性非常好，第二个可以使用非常简洁的命名来命名局部变量，写起来会很优雅。它能够在局部初始UI以及处理逻辑,在团队协作中方便同事快速读懂你的UI布局。
***
参考：[GCC Code Block Evaluation C Extension](http://nshipster.com/new-years-2014/)