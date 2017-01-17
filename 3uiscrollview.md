#UIScrollView
UIScrollView作为非常常用的控件之一，其主要特性就是可以滚动。手机屏幕只有这么大，想要看到超出屏幕范围的内容，UIScrollView就诞生了，通过左右上下滑动我们就可以看到屏幕大小以外的内容。


###UIScrollView基本用法
- 创建scrollView
```objc
    UIScrollView *scrollView = [[UIScrollView alloc]initWithFrame:CGRectMake(50, 100, 300, 400)];
    scrollView.backgroundColor = [UIColor redColor];
    [self.view addSubview:scrollView];
```
此时scrollView还不能滚动，因为其默认滚动范围为{w:0, h:0}，需要指定它的滚动内容大小，那什么是滚动内容的大小呢？它是scrollView的一个属性：CGSize  contentSize，即一个矩形区域，表示通过滑动UIScrollView能看到的视图范围，
如下图暗色区域(包括UIScrollView部分)所示。假想它是一个真实存在的view,默认情况下其左上角与UIScrollView左上角重合。contentSize.width、contentSize.height为其宽高。
![](/assets/pic3-1.png)

