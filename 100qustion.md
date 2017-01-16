#Question
记录在iOS学习过程中的一些疑问。

#####1. IBOutlet连出来的视图属性为什么被设置成weak而不是strong?

要理解为什么要设置成weak，首先要明确strong和weak的区别，被strong修饰的属性可以理解为强指针，被引用的对象引用计数+1，而weak修饰的对象则不会。

当weak属性btn引用对象myButton，即执行代码行(1)后myButton立即释放
```objc
@interface ViewController ()
@property (weak, nonatomic) IBOutlet UIButton *btn;
@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];
    //设想myButton为IB上创建的button;btn属性引用myButton,btn属性为weak      
    即弱指针，myButton引用计数不变
    self.btn = myButton; //(1)
}
@end

```
```objc
@property (nonatomic,strong) UIButton * button;

```



