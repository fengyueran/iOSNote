#UIButton
UIButton作为最常用的控件之一，其继承了父类UIView的属性和方法，同时由于其继承了UIControl使其与UIView区别开来，可以接收touch事件。

##UIButton常用方法

- UIButton的创建

```objc
    UIButton *button = [[UIButton alloc]initWithFrame:CGRectMake(100, 100, 100, 100)];
    button.titleLabel.text = @"button";
    button.backgroundColor = [UIColor redColor];
    [self.view addSubview:button];
    
```
效果如下图，可以看到button的title并没有显示出来,why?
<div align="center">
<img src = "assets/pic2-1.png" width="400" height="360"</>
</div>


于是默默的打印了button...
```objc
<UIButtonLabel: 0x7fccf9725290; frame = (0 0; 0 0);
 text = 'button'; hidden = YES; opaque = NO; userInteractionEnabled = NO; 
 layer = <_UILabelLayer: 0x7fccf97258e0>>
```
可以看到直接用点语法设置title时button上的label frame为0，且hidden属性为YES，title自然不能显示。
