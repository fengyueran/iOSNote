#KVO
KVO即key-value-observing,键值观察。KVO提供了一种机制，指定一个被观察对象(如student对象)，当被观察对象student的某个属性(如name)发生改变时观察者对象(如teacher)会收到通知，同时作出相应处理(这孩子，怎么能随便改名呢，把你家长叫来)。

**1.**KVO的应用
- 注册观察者，实施监听:

```objc
    [student addObserver:teacher forKeyPath:@"name" options:NSKeyValueObservingOptionNew context:nil];

```