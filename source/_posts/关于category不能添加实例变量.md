---
title: 关于category不能添加实例变量
toc: true
date: 2016-06-23 00:25:04
tags: 
     - category
     - iOS
categories: technology
description: category 不能添加实例变量的原因
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
# Category
## 说明
在编写面向对象的程序时，我们经常想为现有的类添加一些新的行为。例如，你设计了一种新的Car，因此需要创建Car类的子类并添加一些有趣的功能。为已经存在的类添加行为时，通常我们采用添加创建子类的方法。<br>
不过有时子类并不方便。比方说，你想为`NSString`类添加一个新的行为，但是`NSString`实际是一个类簇的表面形式，因而为这样的类创建子类会非常困难。<br>
利用Object-c的动态运行分配机制，我们可以为现有的类添加方法。这些方法就是Object-c中的`Category`
## 定义
	struct objc_category {
	    //category名称
	    char *category_name                                      
	    //添加category的类名称
	    char *class_name        
	    //实例方法列表                                
	    struct objc_method_list *instance_methods                
	    //类方法列表
	    struct objc_method_list *class_methods                   
	    //协议列表
	    struct objc_protocol_list *protocols                     
	}                                                            

## Category的优势
1. 利用Category分散实现代码
2. 通过Category创建前向应用

# Extension
## 定义
Extension就是匿名Category，但是又有着很大的不同。

# 对比
1. Category可以写在单独一个文件内，也可以写在与之关联的类里面
2. Extension必须写在实现文件里  
3. Category声明的方法可以不实现.
4. Extension里声明的方法必须实现
5. Category不能添加实例变量
6. Extension可以添加实例变量
7. Category是运行时才会执行
8. Extension是编译时就会和关联的类一起编译


## 为什么Category不能添加实例变量而Extension可以
其实Category有两个局限性。第一个就是无法向类中添加实例变量，因为Category没有空间容纳实例变量。
第二个就是名称冲突，也就是Category中的方法与现有的方法重名。当发生冲突时，类别具有更高的优先级，我们的Category将完全取代初始化方法，导致初始化方法不再可用。<br>
为什么Category没有空间容纳实例变量呢？<br>
从上面Category说明中我们了解到Category运行时工作的，此时一个类的对象的内存已经布局好了，如果此时向一个类的对象添加实例变量就会扰乱内存布局。因此我们从Category的定义中看到没有容纳实例变量的变量，只有Category名称，类名称，实例方法列表，类方法列表，协议列表<br>
而Extension为什么就可以呢？<br>
通过上面的对比我们知道Extension是编译时就会和关联的类一起编译，因此可以。
## 通过runtime向类关联对象
虽然Category不能添加实例变量，但是我们可以利用runtime中的关联对象（Associated Object）变相在运行时向一个对象加变量  
# <div align=center>**打赏**</div>
<div align=center>
![image](http://cl.ly/3w1c3s0A0w2x/weichat_alipay_pay_middle.jpeg)
