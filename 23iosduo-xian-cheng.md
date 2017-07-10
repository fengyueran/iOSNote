 ###iOS 并发编程 4 种方式
 
 - Pthreads
 - NSThread
 - GCD
 - Operation && Operation Queues
 
**1) Pthreads**
>POSIX线程（POSIX threads），简称Pthreads，是线程的POSIX标准。该标准定义了创建和操纵线程的一整套API。在类Unix操作系统（Unix、Linux、Mac OS X等）中，都使用Pthreads作为操作系统的线程。
跨平台，适用于多种操作系统，可移植性强。

是一套纯C语言的通用API，且线程的生命周期需要程序员自己管理，使用难度较大，笔者不建议使用。

下面简单给出使用 Pthreads 并发编程的范例代码
```
#import <pthread.h>

@interface PthreadLoadViewController ()

@end

@implementation PthreadLoadViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
}

- (IBAction)load:(UIButton *)sender {
    pthread_t thread;
    //创建一个线程并自动执行
    pthread_create(&thread, NULL, start, NULL);
}

void *start(void *data){
    NSLog(@"==current==%@",[NSThread currentThread]);
    return NULL;
}
@end
```
使用起来非常困难，需要开发者自己管理 pthread_t 的声明和销毁，一不小心就有可能出问题，比如上面的代码没有销毁 pthread_t thread 这条线程。

**2) NSThread**
>NSThread是 Objective-C 的基础框架的一部分，并为开发者提供一种方法来创建和管理线程。

特点：

- 基于OC语言的API，使得其简单易用（针对 Pthreads 而言），面向对象操作
- 线程的生命周期由程序员管理，偶尔使用（多用于 Debug）

>Ps : 首先，iOS 开发中能遇到的两个线程对象: pthread_t 和 NSThread。过去苹果有份文档/tn/tn2028.html)标明了 NSThread 只是 pthread_t 的封装，但那份文档已经失效了，现在它们也有可能都是直接包装自最底层的 mach thread。苹果并没有提供这两个对象相互转换的接口，但不管怎么样，可以肯定的是 pthread_t 和 NSThread 是一一对应的。比如，你可以通过 pthread_main_thread_np() 或 [NSThread mainThread] 来获取主线程；也可以通过 pthread_self() 或 [NSThread currentThread] 来获取当前线程。

实例：
```
//第一种：动态创建线程,先创建NSThread的实例，然后再启动线程。这又称为动态创建方式
-(void)dynamicCreateThread{
    NSThread *thread = [[NSThread alloc]initWithTarget:self selector:@selector(loadImageSource:) object:imgUrl];
    [thread start];
}

//第二种：直接创建并启动线程。这个即OS X v10.5前支持的方式，又称静态创建方式。
-(void)staticCreateThread{
    [NSThread detachNewThreadSelector:@selector(loadImageSource:) toTarget:self withObject:imgUrl];
}

//第三种方式：隐式的创建并启动线程。本质是通过NSObject类的实例方法performSelectorInBackground: withObject:创建一个后台线程执行特定方法。
-(void)implicitCreateThread {
    [self performSelectorInBackground:@selector(loadImageSource:)  withObject:imgUrl];
}
```