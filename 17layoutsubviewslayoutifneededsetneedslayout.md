在看别人代码的时候时常会看到layoutSubviews方法，于是查阅了一些资料作一个记录。

**1.layoutSubviews()**

主要是为了布局子空间，如下：
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
