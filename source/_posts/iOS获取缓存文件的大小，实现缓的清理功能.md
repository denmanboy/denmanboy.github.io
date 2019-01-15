---
title: iOS获取缓存文件的大小，实现缓的清理功能
toc: true
date: 2013-09-04 18:54:21
tags:
       - iOS
       - 缓存 
categories: technology
description: 移动应用在处理网络资源时，一般都会做离线缓存处理，其中以图片缓存最为典型，其中很流行的离线缓存框架为SDWebImage。
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
移动应用在处理网络资源时，一般都会做离线缓存处理，其中以图片缓存最为典型，其中很流行的离线缓存框架为SDWebImage。

但是，离线缓存会占用手机存储空间，所以缓存清理功能基本成为资讯、购物、阅读类app的标配功能。

今天介绍的离线缓存功能的实现，主要分为缓存文件大小的获取、删除缓存文件的实现。


获取缓存文件的大小

由于缓存文件存在沙箱中，我们可以通过NSFileManager API来实现对缓存文件大小的计算。
``` objc	
//  CalculateFileSize.h
//  CalculateCache
//  Created by dengyanzhou on 15/3/6.
//  Copyright (c) 2015年 mobby. All rights reserved.

#import <Foundation/Foundation.h>
@interface CalculateFileSize : NSObject //单利
//类方法   
+ (instancetype)defaultCalculateFileSize;
// 计算单个文件大小
- (float)fileSizeAtPath:(NSString*)path;
// 计算目录大小
- (float)folderSizeAtPath:(NSString*)path;
// 清除文件
- (void)clearCache:(NSString *)path;
@end

//  CalculateFileSize.m
//  CalculateCache
//  Created by dengyanzhou on 15/3/6.
//  Copyright (c) 2015年 mobby. All rights reserved.

#import "CalculateFileSize.h"
@implementation CalculateFileSize
//单利
+ (instancetype)defaultCalculateFileSize
{
    static CalculateFileSize *calculateFileSize = nil;
    @synchronized(self)  {
        if (!calculateFileSize) {
            calculateFileSize = [[self alloc]init];;
        }
    }
    return calculateFileSize;
}
//计算单个文件大小返回值是M
- (float)fileSizeAtPath:(NSString *)path{
    NSFileManager *fileManager=[NSFileManager defaultManager];
    if([fileManager fileExistsAtPath:path]){
        long long size = [fileManager attributesOfItemAtPath:path error:nil].fileSize;
        // 返回值是字节 B K M
        return size/1024.0/1024.0;
    }
    return 0;
}
//计算目录大小
- (float)folderSizeAtPath:(NSString *)path{
    NSFileManager *fileManager = [NSFileManager defaultManager];
    float folderSize;
    if ([fileManager fileExistsAtPath:path]) {
        NSArray *childerFiles=[fileManager subpathsAtPath:path];
        for (NSString *fileName in childerFiles) {
            NSString *absolutePath = [path stringByAppendingPathComponent:fileName];
            // 计算单个文件大小
            folderSize += [self fileSizeAtPath:absolutePath];
        }
        //SDWebImage框架自身计算缓存的实现
        // folderSize+=[[SDImageCache sharedImageCache] getSize]/1024.0/1024.0;
        return folderSize;
    }
    return 0;
}
//清理缓存文件
//同样也是利用NSFileManager API进行文件操作，SDWebImage框架自己实现了清理缓存操作，我们可以直接调用。
- (void)clearCache:(NSString *)path{
    NSFileManager *fileManager = [NSFileManager defaultManager];
    if ([fileManager fileExistsAtPath:path]) {
        NSArray *childerFiles=[fileManager subpathsAtPath:path];
        for (NSString *fileName in childerFiles) {
            //如有需要，加入条件，过滤掉不想删除的文件
            NSString *absolutePath = [path stringByAppendingPathComponent:fileName];
            [fileManager removeItemAtPath:absolutePath error:nil];
        }
    }
    // SDImageCache 自带缓存
    //[[SDImageCache sharedImageCache] cleanDisk];
}
@end

// 主函数调用
//  main.m
//  CalculateCache
//  Created by dengyanzhou on 15/3/6.
//  Copyright (c) 2015年 mobby. All rights reserved.
#import "CalculateFileSize.h"
#import <Foundation/Foundation.h>
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        CalculateFileSize *calculatefileSize = [CalculateFileSize defaultCalculateFileSize];
        NSString *path = @"/Users/developer/Desktop/c - c++ 学习计划.xlsx";
        float filSize  =   [calculatefileSize fileSizeAtPath:path];
        NSLog(@"文件大小为%fM" ,filSize);
    }
    return 0;
}
```