---
title: delegate和block使用的场景
date: 2016-06-22 19:24:51
toc: true
tags: iOS
categories: technology
description: 开发过程中该选择 blocks or delegates,当我们需要实现回调的时候，使用哪一种方式比较合适呢？”
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

比较这个两者的使用场景最好的方法就是看Apple-IOSSDK的使用，Apple的文档就是一本用来指导我们如何使用设计模式的指导书。    
我们需要去研究一下Apple分别是在什么情况下使用delegate和block，如果我们发现了Apple做这种选择的套路，我们就可以构建出一些规则，可以帮助在我们在自己的代码中做相同选择。
## delegate举例
我们以最常用的tableView为例,看iOSSDK如何设计和使用delegate的   
### UITableViewDelegate

	@protocol UITableViewDelegate<NSObject, UIScrollViewDelegate>
	@optional
	 - (void)tableView:(UITableView *)tableView willDisplayCell:(UITableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath;
	- (void)tableView:(UITableView *)tableView willDisplayHeaderView:(UIView *)view forSection:(NSInteger)section NS_AVAILABLE_IOS(6_0);
	- (void)tableView:(UITableView *)tableView willDisplayFooterView:(UIView *)view forSection:(NSInteger)section NS_AVAILABLE_IOS(6_0);
	- (void)tableView:(UITableView *)tableView didEndDisplayingCell:(UITableViewCell *)cell forRowAtIndexPath:(NSIndexPath*)indexPath NS_AVAILABLE_IOS(6_0);
	- (void)tableView:(UITableView *)tableView didEndDisplayingHeaderView:(UIView *)view forSection:(NSInteger)section NS_AVAILABLE_IOS(6_0);
	- (void)tableView:(UITableView *)tableView didEndDisplayingFooterView:(UIView *)view forSection:(NSInteger)section NS_AVAILABLE_IOS(6_0);
	 @end  

###  UITableViewDataSource

	@protocol UITableViewDataSource<NSObject>
	@required
	
	- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;
	
	// Row display. Implementers should *always* try to reuse cells by setting each cell's reuseIdentifier and querying for available reusable cells with dequeueReusableCellWithIdentifier:
	// Cell gets various attributes set automatically based on table (separators) and data source (accessory views, editing controls)
	
	- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;
	
	@optional
	
	- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView;              // Default is 1 if not implemented
	
	- (nullable NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section;    // fixed font style. use custom view (UILabel) if you want something different
	- (nullable NSString *)tableView:(UITableView *)tableView titleForFooterInSection:(NSInteger)section;
	
	// Editing
	
	// Individual rows can opt out of having the -editing property set for them. If not implemented, all rows are assumed to be editable.
	- (BOOL)tableView:(UITableView *)tableView canEditRowAtIndexPath:(NSIndexPath *)indexPath;
	
	// Moving/reordering
	
	// Allows the reorder accessory view to optionally be shown for a particular row. By default, the reorder control will be shown only if the datasource implements -tableView:moveRowAtIndexPath:toIndexPath:
	- (BOOL)tableView:(UITableView *)tableView canMoveRowAtIndexPath:(NSIndexPath *)indexPath;
	
	@end

## delegate分析
- 大多数delegate  都会好几个数据源
从上面的tableViewDataSource 我们可以看到，一个tableView如何展示，需要询问代理，有几个section，每个section里面有几行，行高是多少。当然达到这样的需求，也可以为对应每一个事件注册相应的block，显然这种方式是不合理的
- 大多数delegate 都有多个回调，用来捕获对象的行为过程  
  特别是系统提供的一些控件来，都有很多delegate回调方法，用来捕获对象的一些行为动作过程，像`UITextField`,`UISearchBar`,这些控件的一些行为，通过delegate方法告诉delegate控件的一些行为状态。
  
# Block举例

```objc
 [UIView animateWithDuration:<#(NSTimeInterval)#> animations:<#^(void)animations#>   
 completion:<#^(BOOL finished)completion#>]           
 
 dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(<#delayInSeconds#> * NSEC_PER_SEC)),  
   dispatch_get_main_queue(), ^{
        <#code to be executed after a specified delay#>
    });
```

# block分析
- 大多数block就是执行一次的任务
- 大多数block就是用来做callBack
# 对比
从上面我们可以看到delegate，是有多个方法调用，是分步骤的，因此我们可以总结到，delegate是面向过程的。  
block是执行一次任务，是面向结果的。  
因此，在我们写代码时，如果是面向过程的，我们就使用delegate，是要获取结果的就使用block。
