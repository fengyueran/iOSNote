#Question
在iOS学习过程中总会遇到这样那样的疑问，而且总是遗忘，好记性不如烂笔头，在这里将自己遇到的问题一个一个添加进来，作为自己的工具书。

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

#####2. 为什么NSString属性(或NSArray，NSDictionary）常用copy修饰?
常看到NSString属性用copy关键字来修饰，是不是NSString用copy最好呢？
凡事没有绝对，copy也一样，之所以用copy是为了NSString属性的安全，如下：
当myStr用strong修饰，其初始化来自于NSMutableString:yourStr，当yourStr改变时myStr也跟着改变了，而用copy修饰的时候，给myStr赋值时yourStr会拷贝一份不可变的字符串(深拷贝、内容拷贝)，因此再对来源进行修改时，myStr也不会受影响。
```objc
@property (nonatomic, strong) NSString *myStr;
- (void)viewDidLoad {
    [super viewDidLoad];
    NSMutableString *yourStr = [[NSMutableString alloc]initWithString:@"1"];
    self.myStr = yourStr;
    [yourStr appendString:@"2"];
    NSLog(@"str=%@",yourStr);
    NSLog(@"self.myStr=%@",self.myStr);
}
/*strong时的打印信息：
str=12
self.myStr=12

copy时的打印信息：
str=12
self.myStr=1
*/

```
既然copy很安全为什么不都用copy呢？事实上在执行self.myStr = yourStr时有这样的操作self.myStr = [yourStr copy];在copy里边会对来源进行判断
if ([yourStr isMemberOfClass:[NSMutableString class]])
如果是不可变string就进行浅拷贝，不产生新的对象，如果是可变string就进行深拷贝，产生新的对象。这个判断操作在NSString很多时开销是比较大的，因此如果能确定来源是不可变的类型，如@“不可变”，就用strong，否则用copy。







