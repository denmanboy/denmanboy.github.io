---
title: iOS判断是否第一次使用且更新后还是第一次
toc: true
tags: iOS
categories: technology
date: 2015-08-10 19:24:55
description: IOS开发判断是否第一次使用软件且下次app自动更新后再次打开还是第一次使用
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

对于用户来讲，在第一次打开软件的时候，最好有个引导页，告诉用户软件怎么使用，或者本次软件的主要的更新在哪儿，提高用户的软件体验。

下面有有两种方法

1.本地存储一个bool变量判断是不是第一次启动,若是第一次启动就打开引导页。  
2.本地存储版本号，判断当前版本号是否大于本地保存的版本号，如果大于则启动引导页。
那么这两种那个好呢，显然第二种较好，因为第一种方法，若是版本升级的时候，我们往往还要引导用户进入引导页，但本地已经保存了不是第一次启动，因此当版本升级的时候就不会再次进入引导页。第二种方法，升级的时候，本次保存的还是上个版本的版本号，因此升级后当前版本号肯定大于上个版本号，因此则会进入引导页。

不废话了，上代码！
```objc	
//当前版本
NSString *currentVersion = [[[NSBundlemainBundle]infoDictionary]objectForKey:@"CFBundleShortVersionString"];			
//本地存储的版本
NSString *saveVersion  = [[NSUserDefaultsstandardUserDefaults]objectForKey:@"CFBundleShortVersionString"];   
if ([currentVersionfloatValue] > [saveVersion floatValue]) 
{
			
[[NSUserDefaults standardUserDefaults]setObject:currentVersion forKey:@"CFBundleShortVersionString"];
[[NSUserDefaults standardUserDefaults]synchronize];
//TODO:   
		}
```














