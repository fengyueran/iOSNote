#Question
记录在iOS学习过程中的一些疑问。

#####1. IBOutlet连出来的视图属性为什么被设置成weak而不是strong?

要理解为什么要设置成weak，首先要明确strong和weak的区别，被strong修饰的属性可以理解为强指针，被引用的对象引用计数+1，而weak修饰的对象则不会。

如下，当通过IBOutlet连线得到的weak属性btn引用IB创建的对象myButton后，即执行代码行(1)后，因为只有弱指针引用mybutton,myButton应立即释放，但事实并非如此，myButton并未释放，why?可以想象应该是还有其他某个强指针引用着myButton。事实是在IB上创建的button存在着这样一种引用关系UIViewController->UIView->subView->UIButton，也就是说myButton的父类对它进行了强引用，只要父类view
还在，myButton就不会被释放，当父类view都被释放了，作为儿子的myButton也就没有存在的价值，所以用weak是没有问题的，当然你也可以用strong，凭爷高兴。

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



