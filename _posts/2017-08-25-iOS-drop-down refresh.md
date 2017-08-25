---
layout: post
title:  "iOS-下拉刷新"
date: 2017-05-04
desc: "iOS-下拉刷新"
keywords: "iOS"
categories: [iOS]
tags: [iOS,Objective-C]
icon: icon-html
---

    下拉刷新模块基本上已经是app中不可缺少的模块，而且网络上开源的多样的下拉刷新控件已经很多，一般随便到网上下载一个改改就能放到自己的项目中。
    假如想自己开发一个下拉刷新的模块集成到自己的项目中应该注意哪些呢？

    是否模块化，方便集成；
    刷新样式是否方便替换；
    能否支持ScrollView；

    思路：
    模块化，这就需要下拉刷新操作过程中的一些状态改变都在RefreshView中进行；
    方便集成，我采用直接addsubview的方式，然后再用delegate回调。
    
    不同的App下拉刷新的样式极有可能是不同的，那么为了方便自己在下一个app项目中能够在快速的更换样式而不需要修改原有下拉逻辑，这时我想到的是直接更换下拉区域的view。
    
    大部分情况下拉刷新都是用在UITableView中，设计这个模块的时候也打算针对tableView，后来想想在特定的UI页面中，可能不是tableview而是webview或者scrollView需要下拉刷新功能。所以我决定将UIScrollView类进行扩展。
    
    实现：
    定义下拉刷新过程中的状态

    typedef NS_ENUM(NSInteger, PGRefreshState) {
    PGRefreshState_Normal = 0,
    PGRefreshState_Pulling,
    PGRefreshState_Loading,
    PGRefreshState_Stopped,
    PGRefreshState_Dragging
    };

>###refreshView在满足触发刷新条件时，通知外部作出响应（加载网络数据），还需知道外部情况（是否正在加载数据）。
    我采用delegate的方式

    @protocol PGRefreshViewDelegate <NSObject>
    @required
    - (BOOL)isLoadingData:(UIScrollView *)scrollView;
    - (void)willStartRefresh:(UIScrollView *)scrollView;
    
    @end

>###定义BaseRefreshView类，不同样式的下拉刷新都继承它，子类只需根据状态作相应的UI动作。

    @interface PGBaseRefreshView : UIView
    @property(nonatomic, assign)float mPullingDistance;
    @property(nonatomic, assign)float mLoadingStopInset;
    @property(nonatomic, assign)PGRefreshState mState;
    @property(nonatomic, weak)id<PGRefreshViewDelegate> delegate;
    @property(nonatomic, strong)UIScrollView *mScrollView;

    - (void)setState:(PGRefreshState)state;
    
    - (void)startRefresh:(UIScrollView *)scrollView;
    
    - (void)stopRefresh:(UIScrollView *)scrollView;
    
    - (void)refreshScrollViewDidScroll:(UIScrollView *)scrollView;
    
    - (void)refreshScrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate;
    
    @end

>###接下来就是对类UIScrollView进行扩展

    #import <UIKit/UIKit.h>
    #import "PGBaseRefreshView.h"
    #import "PGBaseMoreView.h"

    @protocol PGRefreshDelegate <NSObject>

    @required
    /*
    开始刷新
    */
    - (void)willStartRefresh:(UIScrollView *)scrollView;
    /*
    加载更多
    */
    - (void)willStartLoadMore:(UIScrollView *)scrollView;
    
    @end

    #pragma mark -
    @interface UIScrollView (refresh)<PGRefreshViewDelegate, PGMoreViewDelegate>
    
    @property(nonatomic, weak)id<PGRefreshDelegate> refreshDelegate;
    
    @property(nonatomic, strong)PGBaseRefreshView *refreshView;
    @property(nonatomic, assign)BOOL bPullDownEnable;//是否能下拉刷新
    @property(nonatomic, assign)BOOL bLoading;//是否正在加载数据
    
    @property(nonatomic, strong)PGBaseMoreView *moreView;
    @property(nonatomic, assign)BOOL bLoadMoreEnable;//是否能加载更多
    @property(nonatomic, assign)BOOL bLoadMoring;//是否正在加载更多
    @property(nonatomic, assign)BOOL bNeedLoadMoreData;//是否需要加载更多
    
    @property(nonatomic, assign)NSInteger nNumOfPage;//每次加载的数量
    @property(nonatomic, assign)NSInteger nPageIndex;//当前的页数
    @property(nonatomic, assign)NSInteger nTotal;//总记录条数
    
    /*
    开始刷新
    */
    - (void)startRefresh;
    /*
    结束刷新
    */
    - (void)endRefreshing;
    /*
    结束加载更多
    */
    - (void)endLoadMoring;
    
    #pragma mark -
    - (void)pgScrollViewDidScroll;
    - (void)pgScrollViewDidEndDragging;
    
    #pragma mark -
    /*
    刷新数据
    */
    - (void)reRefreshData;
    
    @end
    
>###添加方式(部分代码段)
    
    table.bPullDownEnable = bEnableRefreshHead;
    table.bLoadMoreEnable = bLoadMore;
    table.nNumOfPage = self.nNumOfPage;
    if(bEnableRefreshHead) {
    table.refreshDelegate = self;
    table.refreshView = [[PGRefreshRotate alloc] initWithFrame:CGRectMake(0,-80,CGRectGetWidth(table.frame),80)];
    }
    
    if(bLoadMore) {
    table.refreshDelegate = self;
    table.moreView = [[PGSimpleMoreView alloc] initWithFrame:CGRectMake(0, table.contentSize.height, CGRectGetWidth(table.frame), 40)];
    table.moreView.hidden = YES;
    }
    
>###这时你会发现上面代码如何快速更换下拉刷新样式了吧。

    #pragma mark - UIScrollViewDelegate
    - (void)scrollViewDidScroll:(UIScrollView *)scrollView
    {
    [scrollView pgScrollViewDidScroll];
    }
    
    -(void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate
    {
    [scrollView pgScrollViewDidEndDragging];
    }
