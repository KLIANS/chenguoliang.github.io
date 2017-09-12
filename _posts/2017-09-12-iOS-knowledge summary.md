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

>一、调用代码使APP进入后台，达到点击Home键的效果。（私有API）

    [[UIApplication sharedApplication] performSelector:@selector(suspend)];

    suspend的英文意思有：暂停; 悬; 挂; 延缓;

>二、带有中文的URL处理。大概举个例子，类似下面的URL，里面直接含有中文，可能导致播放不了，那么我们要处理一个这个URL，因为他太操蛋了，居然用中文。

    http://static.tripbe.com/videofiles/视频/我的自拍视频.mp4
    NSString *path  = (__bridge_transfer NSString *)CFURLCreateStringByReplacingPercentEscapesUsingEncoding(NULL,  (__bridge CFStringRef)model.mp4_url,                                                                         CFSTR(""),                                                                                                    CFStringConvertNSStringEncodingToEncoding(NSUTF8StringEncoding));

>三、获取UIWebView的高度 ,个人最常用的获取方法，感觉这个比较靠谱

    - (void)webViewDidFinishLoad:(UIWebView *)webView  {  
    CGFloat height = [[webView stringByEvaluatingJavaScriptFromString:@"document.body.offsetHeight"] floatValue];  
    CGRect frame = webView.frame;  
    webView.frame = CGRectMake(frame.origin.x, frame.origin.y, frame.size.width, height);  
    } 

>四、给UIView设置图片（UILabel一样适用）
     第一种方法： 
     利用的UIView的设置背景颜色方法，用图片做图案颜色，然后传给背景颜色。
         UIColor *bgColor = [UIColor colorWithPatternImage: [UIImage imageNamed:@"bgImg.png"];
         UIView *myView = [[UIView alloc] initWithFrame:CGRectMake(0,0,320,480)];
         [myView setBackGroundColor:bgColor];
    
     第二种方法：
     UIImage *image = [UIImage imageNamed:@"yourPicName@2x.png"];
     yourView.layer.contents = (__bridge id)image.CGImage;
     //设置显示的图片范围
     yourView.layer.contentsCenter = CGRectMake(0.25,0.25,0.5,0.5);//四个值在0-1之间，对应的为x，y，width，height。


>五、去掉UITableView多余的分割线
    yourTableView.tableFooterView = [UIView new];

>六、调整cell分割线的位置，两个方法一起用，暴力解决，防脱发
     -(void)viewDidLayoutSubviews {

         if ([self.mytableview respondsToSelector:@selector(setSeparatorInset:)]) {
            [self.mytableview setSeparatorInset:UIEdgeInsetsMake(0, 0, 0, 0)];

         }
         if ([self.mytableview respondsToSelector:@selector(setLayoutMargins:)])  {
            [self.mytableview setLayoutMargins:UIEdgeInsetsMake(0, 0, 0, 0)];
         }
     }

     #pragma mark - cell分割线
     - (void)tableView:(UITableView *)tableView willDisplayCell:(UITableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath
     {
         if ([cell respondsToSelector:@selector(setSeparatorInset:)]){
             [cell setSeparatorInset:UIEdgeInsetsMake(0, 0, 0, 0)];
         }
         if ([cell respondsToSelector:@selector(setLayoutMargins:)]) {
            [cell setLayoutMargins:UIEdgeInsetsMake(0, 0, 0, 0)];
         }
     }


>七、UISearchController和UISearchBar的Cancle按钮改title问题，简单粗暴
     - (BOOL)searchBarShouldBeginEditing:(UISearchBar *)searchBar
     {
        searchController.searchBar.showsCancelButton = YES;
        UIButton *canceLBtn = [searchController.searchBar valueForKey:@"cancelButton"];
        [canceLBtn setTitle:@"取消" forState:UIControlStateNormal];
        [canceLBtn setTitleColor:[UIColor colorWithRed:14.0/255.0 green:180.0/255.0 blue:0.0/255.0 alpha:1.00] forState:UIControlStateNormal];
        searchBar.showsCancelButton = YES;
        return YES;
     }

>八、UITableView收起键盘
     一个属性搞定，效果好（UIScrollView同样可以使用） 
     以前是不是觉得[self.view endEditing:YES];很屌，这个下面的更屌。 
     yourTableView.keyboardDismissMode = UIScrollViewKeyboardDismissModeOnDrag;
     另外一个枚举为UIScrollViewKeyboardDismissModeInteractive，表示在键盘内部滑动，键盘逐渐下去。

>九、NSTimer  
    1、NSTimer计算的时间并不精确 
    2、NSTimer需要添加到runLoop运行才会执行，但是这个runLoop的线程必须是已经开启。 
    3、NSTimer会对它的tagert进行retain，我们必须对其重复性的使用intvailte停止。target如果是self（指UIViewController），那么VC的retainCount+1，如果你不释放NSTimer，那么你的VC就不会dealloc了，内存泄漏了。


>十、UIViewController没用大小(frame) 
    经常有人在群里问：怎么改变VC的大小啊？ 
    瞬间无语。（只有UIView才能设置大小，VC是控制器啊，哥！）

>十一、用十六进制获取UIColor（类方法或者Category都可以，这里我用工具类方法）
     + (UIColor *)colorWithHexString:(NSString *)color
     {
        NSString *cString = [[color stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] uppercaseString];

        // String should be 6 or 8 characters
        if ([cString length] < 6) {
          return [UIColor clearColor];
        }

        // strip 0X if it appears
        if ([cString hasPrefix:@"0X"])
            cString = [cString substringFromIndex:2];
        if ([cString hasPrefix:@"#"])
          cString = [cString substringFromIndex:1];
        if ([cString length] != 6)
           return [UIColor clearColor];

        // Separate into r, g, b substrings
        NSRange range;
        range.location = 0;
        range.length = 2;

        //r
        NSString *rString = [cString substringWithRange:range];

        //g
        range.location = 2;
        NSString *gString = [cString substringWithRange:range];

        //b
        range.location = 4;
        NSString *bString = [cString substringWithRange:range];

        // Scan values
        unsigned int r, g, b;
        [[NSScanner scannerWithString:rString] scanHexInt:&r];
        [[NSScanner scannerWithString:gString] scanHexInt:&g];
        [[NSScanner scannerWithString:bString] scanHexInt:&b];

         return [UIColor colorWithRed:((float) r / 255.0f) green:((float) g / 255.0f) blue:((float) b / 255.0f) alpha:1.0f];
     }


>十二、获取今天是星期几
     + (NSString *) getweekDayStringWithDate:(NSDate *) date
     {
        NSCalendar * calendar = [[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar]; // 指定日历的算法
        NSDateComponents *comps = [calendar components:NSWeekdayCalendarUnit fromDate:date];

        // 1 是周日，2是周一 3.以此类推

        NSNumber * weekNumber = @([comps weekday]);
        NSInteger weekInt = [weekNumber integerValue];
        NSString *weekDayString = @"(周一)";
        switch (weekInt) {
                case 1:
                {
                 weekDayString = @"(周日)";
                }
              break;

                case 2:
                {
               weekDayString = @"(周一)";
                 }
              break;

                case 3:
                 {
               weekDayString = @"(周二)";
                }
                break;

                 case 4:
                {
                weekDayString = @"(周三)";
                }
                break;

                 case 5:
                {
               weekDayString = @"(周四)";
                }
                break;

                case 6:
                {
              weekDayString = @"(周五)";
                }
                break;

                case 7:
                {
              weekDayString = @"(周六)";
                }
                break;

               default:
                break;
        }
        return weekDayString;

     }


>十三、UIView的部分圆角问题
    UIView *view2 = [[UIView alloc] initWithFrame:CGRectMake(120, 10, 80, 80)];
    view2.backgroundColor = [UIColor redColor];
    [self.view addSubview:view2];

    UIBezierPath *maskPath = [UIBezierPath bezierPathWithRoundedRect:view2.bounds byRoundingCorners:UIRectCornerBottomLeft | UIRectCornerBottomRight cornerRadii:CGSizeMake(10, 10)];
    CAShapeLayer *maskLayer = [[CAShapeLayer alloc] init];
    maskLayer.frame = view2.bounds;
    maskLayer.path = maskPath.CGPath;
    view2.layer.mask = maskLayer;
    //其中，
    byRoundingCorners:UIRectCornerBottomLeft | UIRectCornerBottomRight
    //指定了需要成为圆角的角。该参数是UIRectCorner类型的，可选的值有：
    * UIRectCornerTopLeft
    * UIRectCornerTopRight
    * UIRectCornerBottomLeft
    * UIRectCornerBottomRight
    * UIRectCornerAllCorners

    从名字很容易看出来代表的意思，使用“|”来组合就好了。


>十四、设置滑动的时候隐藏navigationBar
    navigationController.hidesBarsOnSwipe = Yes;


>十五、iOS画虚线 ,记得先 QuartzCore框架的导入
    #import <QuartzCore/QuartzCore.h>

    CGContextRef context =UIGraphicsGetCurrentContext();  
    CGContextBeginPath(context);  
    CGContextSetLineWidth(context, 2.0);  
    CGContextSetStrokeColorWithColor(context, [UIColor whiteColor].CGColor);  
    CGFloat lengths[] = {10,10};  
    CGContextSetLineDash(context, 0, lengths,2);  
    CGContextMoveToPoint(context, 10.0, 20.0);  
    CGContextAddLineToPoint(context, 310.0,20.0);  
    CGContextStrokePath(context);  
    CGContextClosePath(context);  


>十六、自动布局中多行UILabel，需要设置其preferredMaxLayoutWidth属性才能正常显示多行内容。另外如果出现显示不全文本，可以在计算的结果基础上＋0.5。
     CGFloat h = [model.message boundingRectWithSize:CGSizeMake([UIScreen mainScreen].bounds.size.width - kGAP-kAvatar_Size - 2*kGAP, CGFLOAT_MAX) options:NSStringDrawingUsesLineFragmentOrigin attributes:attributes context:nil].size.height+0.5;


>十七、禁止程序运行时自动锁屏 
    [[UIApplication sharedApplication] setIdleTimerDisabled:YES];


>十八、KVC相关，支持操作符 
    KVC同时还提供了很复杂的函数，主要有下面这些 
    ①简单集合运算符 
    简单集合运算符共有@avg， @count ， @max ， @min ，@sum5种，都表示啥不用我说了吧， 目前还不支持自定义。

    @interface Book : NSObject
    @property (nonatomic,copy)  NSString* name;
    @property (nonatomic,assign)  CGFloat price;
    @end
    @implementation Book
    @end


    Book *book1 = [Book new];
    book1.name = @"The Great Gastby";
    book1.price = 22;
    Book *book2 = [Book new];
    book2.name = @"Time History";
    book2.price = 12;
    Book *book3 = [Book new];
    book3.name = @"Wrong Hole";
    book3.price = 111;

    Book *book4 = [Book new];
    book4.name = @"Wrong Hole";
    book4.price = 111;

    NSArray* arrBooks = @[book1,book2,book3,book4];
    NSNumber* sum = [arrBooks valueForKeyPath:@"@sum.price"];
    NSLog(@"sum:%f",sum.floatValue);
    NSNumber* avg = [arrBooks valueForKeyPath:@"@avg.price"];
    NSLog(@"avg:%f",avg.floatValue);
    NSNumber* count = [arrBooks valueForKeyPath:@"@count"];
    NSLog(@"count:%f",count.floatValue);
    NSNumber* min = [arrBooks valueForKeyPath:@"@min.price"];
    NSLog(@"min:%f",min.floatValue);
    NSNumber* max = [arrBooks valueForKeyPath:@"@max.price"];
    NSLog(@"max:%f",max.floatValue);

    打印结果
    2016-04-20 16:45:54.696 KVCDemo[1484:127089] sum:256.000000
    2016-04-20 16:45:54.697 KVCDemo[1484:127089] avg:64.000000
    2016-04-20 16:45:54.697 KVCDemo[1484:127089] count:4.000000
    2016-04-20 16:45:54.697 KVCDemo[1484:127089] min:12.000000

    NSArray 快速求总和 最大值 最小值 和 平均值

    NSArray *array = [NSArray arrayWithObjects:@"2.0", @"2.3", @"3.0", @"4.0", @"10", nil];
    CGFloat sum = [[array valueForKeyPath:@"@sum.floatValue"] floatValue];
    CGFloat avg = [[array valueForKeyPath:@"@avg.floatValue"] floatValue];
    CGFloat max =[[array valueForKeyPath:@"@max.floatValue"] floatValue];
    CGFloat min =[[array valueForKeyPath:@"@min.floatValue"] floatValue];
    NSLog(@"%f\n%f\n%f\n%f",sum,avg,max,min);


>十九、使用MBProgressHud时，尽量不要加到UIWindow上，加self.view上即可。如果加UIWindow上在iPad上，旋转屏幕的时候MBProgressHud不会旋转。之前有人遇到这个bug，我让他改放到self.view上即可解决此bug。


>二十、强制让App直接退出（非闪退，非崩溃）
     - (void)exitApplication {
        AppDelegate *app = [UIApplication sharedApplication].delegate;
        UIWindow *window = app.window;
        [UIView animateWithDuration:1.0f animations:^{
            window.alpha = 0;
        } completion:^(BOOL finished) {
            exit(0);
        }];
     }


>二十一、Label行间距
     NSMutableAttributedString *attributedString =    
     [[NSMutableAttributedString alloc] initWithString:self.contentLabel.text];
     NSMutableParagraphStyle *paragraphStyle =  [[NSMutableParagraphStyle alloc] init];  
     [paragraphStyle setLineSpacing:3];

    //调整行间距       
     [attributedString addAttribute:NSParagraphStyleAttributeName 
                         value:paragraphStyle 
                         range:NSMakeRange(0, [self.contentLabel.text length])];
     self.contentLabel.attributedText = attributedString;


>二十二、CocoaPods pod install/pod update更新慢的问题 
    pod install –verbose –no-repo-update  
    pod update –verbose –no-repo-update 
    如果不加后面的参数，默认会升级CocoaPods的spec仓库，加一个参数可以省略这一步，然后速度就会提升不少。


>二十三、MRC和ARC混编设置方式 
    在XCode中targets的build phases选项下Compile Sources下选择 不需要arc编译的文件 
    双击输入 -fno-objc-arc 即可 
    MRC工程中也可以使用ARC的类，方法如下： 
    在XCode中targets的build phases选项下Compile Sources下选择要使用arc编译的文件 
    双击输入 -fobjc-arc 即可


>二十四、把tableview里cell的小对勾的颜色改成别的颜色 
    _yourTableView.tintColor = [UIColor redColor];


>二十五、解决同时按两个按钮进两个view的问题 
    [button setExclusiveTouch:YES];
    看见有人用这个来控制UIButton的ExclusiveTouch属性，这样需要在每个控制器都要设置。可用一句话来代替这样的设置，在AppDelegate启动应用时添加 [[UIButton appearance] setExclusiveTouch:YES];


>二十六、修改textFieldplaceholder字体颜色和大小
    textField.placeholder = @"请输入用户名";  
    [textField setValue:[UIColor redColor] forKeyPath:@"_placeholderLabel.textColor"];  
    [textField setValue:[UIFont boldSystemFontOfSize:16] forKeyPath:@"_placeholderLabel.font"];


>二十七、禁止textField和textView的复制粘贴菜单 
    这里有一个误区，很多同学直接使用UITextField，然后在VC里面写这个方法，返回NO，没效果。怎么搞都不行，但是如果用UIPasteboard的话，项目中所有的编辑框都不能复制黏贴了，真操蛋。 
    我们要做的是新建一个类MyTextField继承UITextField，然后在MyTextField的.m文件里重写这个方法，就可以单独控制某个输入框了。
     -(BOOL)canPerformAction:(SEL)action withSender:(id)sender
     {
        if ([UIMenuController sharedMenuController]) {
          [UIMenuController sharedMenuController].menuVisible = NO;
        }
        return NO;
     }


>二十八、如何进入我的软件在app store 的页面，先用iTunes Link Maker找到软件在访问地址，格式为itms-apps://ax.itunes.apple.com/…，然后
     #define  ITUNESLINK   @"itms-apps://ax.itunes.apple.com/..."
     NSURL *url = [NSURL URLWithString:ITUNESLINK];
     if([[UIApplication sharedApplication] canOpenURL:url]){
        [[UIApplication sharedApplication] openURL:url];
     }
如果把上述地址中itms-apps改为http就可以在浏览器中打开了。可以把这个地址放在自己的网站里，链接到app store。 
iTunes Link Maker地址：http://itunes.apple.com/linkmaker


>二十九、二级三级页面隐藏系统tabbar 
1、单个处理
    YourViewController *yourVC = [YourViewController new];
    yourVC.hidesBottomBarWhenPushed = YES;
    [self.navigationController pushViewController:yourVC animated:YES];

2.统一在基类里面处理 
新建一个类BaseNavigationController继承UINavigationController，然后重写 -(void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated这个方法。所有的push事件都走此方法。

     @interface BaseNavigationController : UINavigationController

     @end
     -(void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated{
         if (self.viewControllers.count>0) {
            viewController.hidesBottomBarWhenPushed = YES;
         }
         [super pushViewController:viewController animated:animated];
     }


>三十、取消系统的返回手势
    self.navigationController.interactivePopGestureRecognizer.enabled = NO;


>三十一、修改UIWebView中字体的大小，颜色
1、UIWebView设置字体大小，颜色，字体： 
UIWebView无法通过自身的属性设置字体的一些属性，只能通过html代码进行设置 
在webView加载完毕后，在 
     - (void)webViewDidFinishLoad:(UIWebView *)webView方法中加入js代码  
     NSString *str = @"document.getElementsByTagName('body')[0].style.webkitTextSizeAdjust= '60%'";  
     [_webView stringByEvaluatingJavaScriptFromString:str]; 
或者加入以下代码
     NSString *jsString = [[NSString alloc] initWithFormat:@"document.body.style.fontSize=%f;document.body.style.color=%@",fontSize,fontColor];   
     [webView stringByEvaluatingJavaScriptFromString:jsString]; 


>三十二、NSString处理技巧 
使用场景举例：可以用在处理用户用户输入在UITextField的文本
    //待处理的字符串
    NSString *string = @" A B  CD   EFG\n MN\n";

    //字符串替换，处理后的string1 ＝ @"ABCDEF\nMN\n";
    NSString *string1 = [string stringByReplacingOccurrencesOfString:@" " withString:@""];

    //去除两端空格(注意是两端),处理后的string2 ＝ @"A B  CD   EFG\n MN\n";
    NSString *string2 = [string stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceCharacterSet]];

    //去除两端回车(注意是两端),处理后的string3 ＝ @" A B  CD   EFG\n MN";
    NSString *string3 = [string stringByTrimmingCharactersInSet:[NSCharacterSet newlineCharacterSet]];

    //去除两端空格和回车(注意是两端),处理后的string4 ＝ @"A B  CD   EFG\n MN";
    NSString *string4 = [string stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]];


未完，后续补充。。。







