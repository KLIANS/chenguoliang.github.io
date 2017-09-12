---
layout: post
title:  "iOS知识点大总结"
date: 2017-09-12
desc: "iOS知识点大总结"
keywords: "iOS"
categories: [iOS]
tags: [iOS,Objective-C]
icon: icon-html
---

记录一些常用和不常用的iOS知识点，防止遗忘丢失。（来源为收集自己项目中用到的或者整理看到博客中的知识点），如有错误，欢迎大家批评指正；如有好的知识点，也欢迎大家联系我，添加上去。谢谢！

***

> 一、调用代码使APP进入后台，达到点击Home键的效果。（私有API）

    [[UIApplication sharedApplication] performSelector:@selector(suspend)];

    suspend的英文意思有：暂停; 悬; 挂; 延缓;

> 二、带有中文的URL处理。大概举个例子，类似下面的URL，里面直接含有中文，可能导致播放不了，那么我们要处理一个这个URL，因为他太操蛋了，居然用中文。

    http://static.tripbe.com/videofiles/视频/我的自拍视频.mp4
    NSString *path  = (__bridge_transfer NSString *)CFURLCreateStringByReplacingPercentEscapesUsingEncoding(NULL,                                                                                                          (__bridge CFStringRef)model.mp4_url,                                                                         CFSTR(""),                                                                                                    CFStringConvertNSStringEncodingToEncoding(NSUTF8StringEncoding));

> 三、获取UIWebView的高度 ,个人最常用的获取方法，感觉这个比较靠谱

    - (void)webViewDidFinishLoad:(UIWebView *)webView  {  
    CGFloat height = [[webView stringByEvaluatingJavaScriptFromString:@"document.body.offsetHeight"] floatValue];  
    CGRect frame = webView.frame;  
    webView.frame = CGRectMake(frame.origin.x, frame.origin.y, frame.size.width, height);  
    } 

> 四、给UIView设置图片（UILabel一样适用）

    *第一种方法： 
    *利用的UIView的设置背景颜色方法，用图片做图案颜色，然后传给背景颜色。
    UIColor *bgColor = [UIColor colorWithPatternImage: [UIImage imageNamed:@"bgImg.png"];
        UIView *myView = [[UIView alloc] initWithFrame:CGRectMake(0,0,320,480)];
    [myView setBackGroundColor:bgColor];
















