#UIScrollView
UIScrollView作为最常用的控件之一，其主要特性就是可以滚动。手机屏幕只有这么大，想要看到超出屏幕范围的内容，UIScrollView就诞生了，通过左右上下滑动我们就可以看到屏幕大小以外的内容。

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

3）contentInset属性
contentSize规定了可滚动视图的区域，contentInset则赠送了额外的滚动区域，如上图中浅蓝色的区域。
```objc
//上左下右分别增加了80，40，30，10的滚动区域
 self.scrollView.contentInset = UIEdgeInsetsMake(80, 40, 30, 10);
```

**3.UIScrollView工作原理**

要了解UIScrollView为什么能够滚动，就需要了解一个视图的位置是由什么决定的
，在[UIView的笔记][1]中已经介绍了，在这里就不重复了。

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

**4.UIScrollView代理**

- 即将开始拖曳的时候调用

```objc
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView {
   NSLog(@"drag begin");
}
```
- 滚动时调用

```objc
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    NSLog(@"scroll");
}
```
- 结束拖曳时调用

并不是最后调用的代理方法，当拖动很快有惯性滑动时会继续调用scrollViewDidScroll方法，只有滚动很慢没有惯性滑动时才是最后调用的方法。

```objc

- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate {
     NSLog(@"drag end");
}
```
- 编程产生的滚动结束时调用

人为拖拽scrollView导致滚动完毕不会调用这个方法。

官方文档:
```objc
The scroll view calls this method at the end of its implementations of the
setContentOffset(_:animated:) and scrollRectToVisible(_:animated:) methods, 
but only if animations are requested.
```

```objc
//调用setContentOffset、scrollRectToVisible方法时调用
- (void)scrollViewDidEndScrollingAnimation:(UIScrollView *)scrollView {
    NSLog(@" programmatic-generated scroll finishes");
}
```
- 减速完毕时调用，必须有减速才调用

```objc
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView {
    NSLog(@"decelerating");
}
```

**5.UIScrollView疑问**
- 如何才能检测UIScrollView滚动动画结束，即看不到滚动的时间？

我们知道ScrollView只要滚动就会调用scrollViewDidScroll方法，假设某个方法在最后调用scrollViewDidScroll后执行，那这个时候scrollView滚动肯定结束了。如下,scollView滚动时不断添加计时异步操作，而scrollViewDidScroll不断被调用，计时操作不断被取消，直到最后一次调用scrollViewDidScroll方法，此时不再进入scrollViewDidScroll方法，即不会执行[NSObject cancelPreviousPerformRequestsWithTarget:self]方法，而进入scrollViewDidEndScrollingAnimation，此时滚动结束。
```objc
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    NSLog(@"scroll");
    [NSObject cancelPreviousPerformRequestsWithTarget:self];
    //ensure that the end of scroll is fired.
    [self performSelector:@selector(scrollViewDidEndScrollingAnimation:) withObject:nil afterDelay:0.3];
}

- (void)scrollViewDidEndScrollingAnimation:(UIScrollView *)scrollView {
   [NSObject cancelPreviousPerformRequestsWithTarget:self];
    NSLog(@"scroll end");
}

```
PS: I am xinghun who is on the road.

[1]:http://www.jianshu.com/p/4f9a8139d0b3





















