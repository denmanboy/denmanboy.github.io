---
title: block和函数指针的区别
toc: true
date: 2015-03-11 00:26:47
tags: 
    - block
    - 函数指针
categories: technology
description: 简单介绍一些block和函数指针的区别
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
# Demo

``` objc
///  main.m
//  C_函数指针_block
//  Created by dengyanzhou on 15/3/11.
//  Copyright (c) 2015年 mobby. All rights reserved.
//

/*
 *方法名称就是方法在内存的入口地址
 */
int add(int add1,int add2)
{
    return add1 + add2;
}
//定义一个指向有两个整型参数，返回值为整型的函数指针类型；
typedef int (*Sum1) (int ,int);
//定义一个叫做Sum2类型参数为两个整型的，返回值为整型的block
typedef int (^Sum2) (int ,int);
#import <Foundation/Foundation.h>
int main(int argc,const char * argv[]) {
    @autoreleasepool {
        //定义一个Sum1类型的变量 sum1指向了有两个参数，返回值为整型的函数也就是说 sum1 就是一个函数指针
        Sum1  sum1 =add;
        //调用sum1指向的函数并打印
        NSLog(@"sum1 = %d",sum1(2 ,3));
        // 用Sum2这种block类型定义一个Sum2类型的block变量 sum2 并赋值
          Sum2 sum2 = ^(int a ,int  b){
            return a + b ;
        };
        //调用sum2 blocl并打印
        NSLog(@"sum2 = %d",sum2(2,3));
        /*函数指针和block的相同和区别
         //相同点
         函数指针和block都是指向了内存一段代码段调用类似
         //不同点
         1.函数指针是一个指针变量这个变量存放的是函数名 也就是函数的首地址赋值时 直接把函数名字赋值给函数指针变量
         这个指针变量一般存储在栈中
         block是一个对象这个对象的生命周期非常复杂 一般存储在栈中，但这个对象copy后的一份存储在堆中,另外block接收是一个完整的代码快,而不是一个随意的变量或者常量

         2.block 能都给引用block以外的变量而 函数指针不可以什么意思呢？
         看这样一段代码
         int c = 2;
         Sum2 sum2 = ^(int a ,int  b){
            return a + b + c;
          };
         c这个变量在宿sum2 这个block变量以外但是 sum2 block内部可以引用
        */

    }
    return0;
}
```
# result  
sum1 = 5  
sum2 = 5