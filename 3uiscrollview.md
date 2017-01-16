#UIScrollView
UIScrollView作为非常常用的控件之一，其主要特性就是可以滚动。手机屏幕只有这么大，想要看到超出屏幕范围的内容，UIScrollView就诞生了，通过左右上下滑动我们就可以看到屏幕大小以外的内容。


###UIScrollView基本用法
- 创建scrollView
```objc
    UIScrollView *scrollView = [[UIScrollView alloc]initWithFrame:CGRectMake(50, 100, 300, 400)];
    scrollView.backgroundColor = [UIColor redColor];
    [self.view addSubview:scrollView];
```
此时scrollView还不能滚动，需要我指定它的滚动内容大小，那什么是滚动内容的大小呢？
![](/assets/pic3-1.png)

