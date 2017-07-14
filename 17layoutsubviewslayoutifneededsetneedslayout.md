在看别人代码的时候时常会看到layoutSubviews方法，于是查阅了相关资料作一个记录。



**1.视图渲染流程**
在讲layoutSubviews等布局方法之前我们需要了解系统布局的一个流程。于是查阅[官方文档][1]，得到视图的一个交互流程如下
![](/assets/pic17-1 drawing_model.png)

上图对应的事件序列如下：

- 用户触摸屏幕
- 硬件传递触摸事件给 UIKit 框架
- UIKit 框架将触摸事件打包成 UIEvent 对象，然后分发给合适的视图
- 事件处理代码会对相应事件作出响应，如： 
  - 更改  frame、bounds、alpha 等属性
 - 调用 setNeedsLayout 方法以标记该视图（或者它的子视图）为需要进行布局更新
 - 调用 setNeedsDisplay 或者 setNeedsDisplayInRect: 方法以标记该视图（或者它的子视图）需要进行重画
 - 通知 Controller 有数据变化

- 如果一个视图的几何结构改变了，UIKit 会根据以下规则更新它的子视图
  a.如果配置了autoresizing规则则根据该负责更新子视图
  b. 如果有layoutSubviews，就调用它更新子视图
  
- 如果任何视图的任何部分被标记为需要重画，UIKit 会要求视图重画自身
- 任何已经更新的视图会与应用余下的可视内容组合在一起，同时被发送到图形硬件去显示
- 图形硬件将已解释内容转化到屏幕上

由上可知layoutSubviews是为了布局子视图而生。

**2.layoutSubviews()**
layoutSubviews什么时候调用，stackoverflow上总结的答案，一一测试，有效。
- init 初始化不会触发 layoutSubviews
- addSubview 会触发 layoutSubviews
- 设置 view 的 frame 会触发 layoutSubviews，当然前提是 frame 的值设置前后发生了变化
- 滚动一个 UIScrollView 会触发 layoutSubviews
- 旋转 Screen 会触发父 UIView 上的 layoutSubviews 事件
- 改变一个 UIView 大小的时候也会触发父 UIView 上的 layoutSubviews 事件


如下：当UIView被addSubview后会自动调用layoutSubviews方法

```
- (instancetype)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        _redView = [[UIView alloc]initWithFrame:CGRectMake(0, 0, 100, 100)];
        [self addSubview:_redView];
        self.backgroundColor = [UIColor blueColor];
    }
    return self;
}

- (void)layoutSubviews {
    [super layoutSubviews];
    self.redView.frame =CGRectMake(0, 0, 100, 100);
    self.redView.backgroundColor = [UIColor redColor];
}
```




[1]:https://developer.apple.com/library/content/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/WindowsandViews/WindowsandViews.html