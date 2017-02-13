#KVO
KVO即key-value-observing,键值观察。KVO提供了一种机制，指定一个被观察对象(如student对象)，当被观察对象student的某个属性(如name)发生改变时观察者对象(如teacher)会收到通知，同时作出相应处理(这孩子，怎么能随便改名呢，把你家长叫来)。

**1.**KVO的应用
- 注册观察者，实施监听:
```objc
    [student addObserver:teacher
              forKeyPath:@"name"
                 options:NSKeyValueObservingOptionNew
                 context:nil];
```
参数说明：

 1）student:被观察者对象
 2）teacher:观察者对象，该对象必须实现    
    observeValueForKeyPath:ofObject:change:context: 方法
 3）forKeyPath:被观察者的属性名，必须和被观察者属性名相同
 4）options:属性配置，有四个值：
 ```objc
 typedef NS_OPTIONS(NSUInteger, NSKeyValueObservingOptions) {
 //接收方法中传入属性变化后的新值，键为NSKeyValueChangeNewKey
    NSKeyValueObservingOptionNew = 0x01,
    //接收方法中传入属性变化前的旧值，键为NSKeyValueChangeOldKey
    NSKeyValueObservingOptionOld = 0x02,
    //注册之后会立刻调用接收方法，可以在程序第一次运行时做一些初始化操作，
    如果配置了NSKeyValueObservingOptionNew，change参数内容会包含新值
    NSKeyValueObservingOptionInitial NS_ENUM_AVAILABLE(10_5, 2_0) = 0x04,
    //接收方法会在属性变化前后分别调用一次，变化前的通知change参数包含键值对：notificationIsPrior = 1。
    NSKeyValueObservingOptionPrior NS_ENUM_AVAILABLE(10_5, 2_0) = 0x08
 }
 ```
 5）context:接收一个C指针，指向希望监听的属性。如：&self->_name
 
- 在回调方法处理属性变化

 ```objc
 - (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context {
    if (context ==  &PrivateKVOContext) {
        if ([keyPath isEqualToString:@"name"]) {
            NSString *oldName = change[NSKeyValueChangeOldKey];
            NSString *newName = change[NSKeyValueChangeNewKey];
        }
    }
}
```
 
