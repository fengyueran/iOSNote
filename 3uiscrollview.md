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
                                                       
contentSize表示通过滑动UIScrollView能看到的视图区域，如下图暗色区域(包括可视的UIScrollView部分)所示，假想它是一个真实存在的view,默认情况下其左上角与UIScrollView左上角重合。contentSize.width、contentSize.height为其宽高。
![](/assets/pic3-1.png)
当contentSize的宽高大于UIScrollView的的宽高时，UIScrollView就能够滚动了。
```objc
    //contentSize.width=800,contentSize.height=1000
    scrollView.contentSize = CGSizeMake(800, 1000);
```
2）contentOffset属性
contentoffset属性为CGPoint类型，也就是一个点，它是偏离ScrollView.bounds.origin的位置，如上图暗色区域的左上角O，默认情况O点为CGPointZero，即O点和O'点重合，此时UIScrollView不能向上或左滚动，那儿没啥，你就别滚了!
```objc
//向左向上滚动100point
self.scrollView.contentOffset = CGPointMake(100, 100);
```
**3.UIScrollView工作原理**

要了解UIScrollView为什么能够滚动，就需要了解一个视图的位置是由什么决定的
，在UIView的笔记中已经介绍了，在这里就不重复了。

**有这样的结论：**
 - 一个视图的位置是由父类的bounds坐标系决定的，因此只要修改bounds.origin就可以改变视图呈现的位置。
 - 视图在父类的位置由下面的公式决定:
 
```objc
CompositedPosition.x = View.frame.origin.x - Superview.bounds.origin.x;
CompositedPosition.y = View.frame.origin.y - Superview.bounds.origin.y;

 ```
 
- 当bounds.origin.x,bounds.origin.y为正时视图向左上移动，反之亦然，这就达到了滚动的目的。

事实上contentOffset的set方法类似这样：
```objc
- (void)setContentOffset:(CGPoint)offset
{
    CGRect bounds = [self bounds];
    bounds.origin = offset;
    [self setBounds:bounds];
}
```
也就是说，设置contentOffset实际上就是改变scrollView的bounds.origin，即内容视图父类(scrollView)的bounds。
