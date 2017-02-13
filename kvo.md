#KVO
KVO即key-value-observing,键值观察。KVO提供了一种机制，指定一个被观察对象(如student对象)，当被观察对象student的某个属性(如name)发生改变时观察者对象(如teacher)会收到通知，同时作出相应处理(这孩子，怎么能随便改名呢，把你家长叫来)。

**1.**KVO的应用步骤
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
 5）context:接收一个C指针，可以为kvo的回调方法传值。

- 在回调方法处理属性变化
每当监听的keypath发生改变就会调用该方法：

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
参数说明：
1）keyPath：被监听的keyPath , 用来区分不同的KVO监听；
2）object：被观察对象，可以获得修改后的属性值；
3）change：保存信息改变的字典（可能有旧的值，新的值等）
4）context：上下文，可以用来区分不同的KVO监听。

- 移除观察者
观察者一定要在适当的时候将其移除。
```objc
[student removeObserver:teacher forKeyPath:@"name" context:&PrivateKVOContext];
```

**2.**KVO的简单应用实例
KVO的常用场景是在MVC中同步model和UI，实现这样的需求：点击view的时候更新model的(person)数据并触发UI同步。可以看到应用KVO轻松的监听到模型数据的变化，进而在回调中更新UI。

<div align="center">
<img src = "assets/pic8-1.gif"</>
</div>

 ```objc
@interface ViewController ()
- (IBAction)randomAge:(UIButton *)sender;
@property (weak, nonatomic) IBOutlet UILabel  *ageLabel;
@property (strong, nonatomic) Person *person;
@end

 @implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    self.person = [[Person alloc]init];
    //创建观察者
    [self.person addObserver:self
              forKeyPath:@"myAge"
                 options:NSKeyValueObservingOptionNew
                 context:nil];

 }
 
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context {
    //UI同步
    self.ageLabel.text = [NSString stringWithFormat:@"%@",change[NSKeyValueChangeNewKey]];
}

- (IBAction)randomAge:(UIButton *)sender {
    //更新model数据
    self.person.myAge = arc4random() % 100 ;
}

- (void)dealloc {
    //移除观察者
    [self removeObserver:self forKeyPath:@"myAge"];
}
 
 @end
 ```



