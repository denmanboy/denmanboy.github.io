---
title: 关于block的那些事
toc: true
tags: iOS
categories: technology
date: 2018-05-22 11:32:19
description: block的分类，循环引用，使用技巧
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
# block是Objective-c中对闭包的实现
## 1.什么是闭包
计算机语言中，闭包（closure）是有函数和与其相关的引用环境组合而成的实体；
闭包就是能够读取其他函数内部变量的函数。
block就是Objective-c对闭包的实现，block是iOS4.0+和Mac OSX 10.6+引进的对C语言的扩展。
## 2. 闭包的理解
将函数、函数指针、闭包三者对比起来理解，能加深对闭包的理解。
函数：具有特定功能的代码块。
函数指针：指向函数的指针。
闭包：除具备函数和函数指针的所有功能外，还包括声明它的上下文（如作用域和自由变量等）。
## 3.block和函数指针的关系
block的使用很像函数指针，不过与函数指针最大的不同是Block可以访问函数以外的外部变量的值，换句话说，Block不仅实现函数的功能，还能携带函数的执行环境。
函数指针：void (*fun)(int) //指向参数为int类型返回值为void类型的函数
Block：void (\^fun)(int)
# block的分类及区分方法
## 分类
在Objective-C语言中，一共有3种类型的block
1._NSConcreteGlobalBlock 保存全局的block，不会访问任何外部变量。相当于全部变量，系统会自动释放。
2._NSConcreteStackBlock 保存在栈中的block，当函数返回时会被销毁。相等于局部变量，系统会自动释放.
3._NSConcreteMallocBlock 保存在堆中的block，当引用计数为0时会被销毁。需要手动释放

## 区分方法
如果block实现中没有访问任何“外部”变量（包括局部和全局），该block为GlobalBlock；
如果block 实现中访问了任何“外部”（包括局部和全局），该block 为StackBlock；
对StackBlock 进行拷贝（copy/Block_copy），该block为MallocBlock；
# block的本质
block实际上是指向结构体的指针，编译器会将block的内部代码生成对应的函数。
[block的实现原理](https://www.cnblogs.com/yoon/p/4953618.html)
# block的一些疑问
## 1.block为什么要用copy
block 在创建的时候默认分配的内存实在栈上，而不是堆上,这样的话其本身的作用域属于创建时候的作用域，一旦在创建的作用域之外调用就会导致程序崩溃，所以使用了copy将其拷贝到堆内存上。
## 2.栈上的block为什么不用retain
retain 只是增加了一次引用计数，Block的内存还是在栈上，并没有存在堆上，存在栈上的block 可能随时被系统回收。
## 3.为什么进入Block 的对象引用计数要加1
block 一般执行的是回调，因此block并不知道其中的对象obj创建后会在什么时候释放，为了不在block使用obj之前，对象已经释放，block就retain了obj一次。
# block中的继承关系
``` objc
    int a = 10;
    void(^block1)(void) = ^(void){
        NSLog(@"这是全局block");
    };
    block1();
    NSLog(@"全局block parentClassHierarchy = %@",[[block1 class] parentClassHierarchy]);
    block1 = [block1 copy];
    NSLog(@"全局block copy parentClassHierarchy = %@",[[block1 class] parentClassHierarchy]);
    void(^block2)(void) = ^(void){
        NSLog(@"a = %d 这是栈block",a);
    };
    block2();
    NSLog(@"栈 block parentClassHierarchy = %@",[[block2 class] parentClassHierarchy]);
    block2 =  [block2 copy];
    NSLog(@"堆 block parentClassHierarchy = %@",[[block2 class] parentClassHierarchy]);
```
## MRC打印结果
![MRC打印结果](/images/MRCBlock_ParentClassHierarchy.png )

## ARC打印结果
![MRC打印结果](/images/ARCBlock_ParentClassHierarchy.png)
通过上面的对比，我们可以看出ARC的函数体内栈block自动copy到堆上
# block 的循环引用

``` objc
    @interface ViewController ()
    @property(nonatomic,copy)void(^myBlock)(void);
    @end
    // 循环引用 self -> block -> self;
    __weak typeof(self) weakSelf = self;
    self.myBlock = ^(void){
        __strong typeof(weakSelf) strongSelf  = weakSelf;
        if (strongSelf) {
            NSLog(@"strongSelf = %@",strongSelf);
        }
    };
    // animations 是栈block
    [UIView animateWithDuration:0.3 animations:^{
        NSLog(@"self = %@",self);
    }];
    
    // 实例方法-
    [self someThingsCompletion:^{
        NSLog(@"self = %@",self);
    }];

    // 单利 
    [[Single shareInstance] somethingCompletion:^{
        NSLog(@"self = %@",self);
    }];

- (void)someThingsCompletion:(void(^)(void))completion
{
    // block 判空 回调 completion 栈block
    !completion ?: completion(); 
}
```
![breakBlock.png](/images/breakBlock.png)
![breakBlock.png](/images/functionBlock.png)

从上面我们可以看出block是函数参数时，就是栈block。
# block使用技巧
## block为空判断
大家知道block若是为空，直接调用就会造成crash。
一般我们会用if语句判断一下,再调用.

``` objc
if(!block){
    block();
}
```
这样还得起if语句，其实我们可以用条件表达式直接判断，调用
`!block ?: block();` 就可以

## block多参数

``` objc
-  (void)blockTest
{
    void(^block1)(void);
    void(^block2)(int a);
    void(^block3)(NSNumber *a, NSString *b);

    //如果block的参数列表为空的话，相当于可变参数（不是void）
    void(^block)(); // 返回值为void，参数可变的block
    block = block1; // 正常
    block = block2; // 正常
    block = block3; // 正常
    block(@1, @"string");  // 对应上面的block3
    block(@1); // block3的第一个参数为@1，第二个为nil

}

// 这样，block的主调和回调之间可以通过约定来决定block回传回来的参数是什么，有几个。如一个对网络层的调用：
- (void)requestDataWithApi:(NSInteger)api block:(void(^)())block //
{
    if (api == 0) {
        block(1, 2);
    }else if (api == 1) {
        block(@"1", @2, @[@"3", @"4", @"5"]);
    }
}
```













