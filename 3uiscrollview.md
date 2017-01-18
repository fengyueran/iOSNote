#UIScrollView
UIScrollView作为非常常用的控件之一，其主要特性就是可以滚动。手机屏幕只有这么大，想要看到超出屏幕范围的内容，UIScrollView就诞生了，通过左右上下滑动我们就可以看到屏幕大小以外的内容。


###UIScrollView基本用法
**1.创建scrollView**

scrollView可以说是一个能滚动的UIView，因此其创建方式同普通控件类似
```objc
    UIScrollView *scrollView = [[UIScrollView alloc]initWithFrame:CGRectMake(50, 100, 300, 400)];
    scrollView.backgroundColor = [UIColor redColor];
    [self.view addSubview:scrollView];
```
**2.UIScrollView属性**

上边创建的scrollView因为其默认滚动范围为{w:0, h:0}还不能滚动，需要指定它的滚动内容大小，那什么是滚动内容的大小呢？

1）contentSize属性 
                                                       
contentSize表示通过滑动UIScrollView能看到的视图范围，如下图暗色区域(包括可视的UIScrollView部分)所示，假想它是一个真实存在的view,默认情况下其左上角与UIScrollView左上角重合。contentSize.width、contentSize.height为其宽高。
![abjbjkjk](/assets/pic3-1.png)
当contentSize的宽高大于UIScrollView的的宽高时，UIScrollView就能够滚动了。
```objc
    //contentSize.width=800,contentSize.height=1000
    scrollView.contentSize = CGSizeMake(800, 1000);
```
2）contentOffset属性
contentoffset属性为CGPoint类型，也就是一个点，如上图暗色区域的左上角，
