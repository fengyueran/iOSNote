####1. 什么叫线程
 试想有这样一份代码
 ```
- (void)main {
    [self startA];
}

- (void)startA {
    while (true) {
        NSLog(@"I am in A");
    }
    [self startB];
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
          [self startB];
    });
}

- (void)startB {
    while (true) {
        NSLog(@"I am in B");
    }
}

 ```
 这份代码保存在寄存器上，当运行程序时就执行这份代码。
我们知道线程是执行程序最基本的单元，它有自己栈和寄存器，说得再具体一些，线程就是“一个CPU执行的一条无分叉的命令列”。也就是说对于上面这份代码而言执行过程必须是：
  ```
main=>startA=>startB
```
A没有执行完一定不会执行B，因为对于一条线程而言只有一条执行路径main=>A=>B，如此，我们另开一条线程，也就有了另一条执行代码的路径,直接执行dispatch中的代码[self startB];
```
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
          [self startB];
    });

```
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

**3) GCD**
GCD提供了功能强大的任务和队列控制功能，相比于NSOperationQueue更加底层

GCD以block为基本单位，一个block中的代码可以为一个任务。下文中提到任务，可以理解为执行某个block

同时，GCD中有两大最重要的概念，分别是“队列”和“执行方式”。

使用block的过程，概括来说就是把block放进合适的队列，并选择合适的执行方式去执行block的过程。

三种队列：

- 串行队列（先进入队列的任务先出队列，每次只执行一个任务）
- 并发队列（依然是“先入先出”，不过可以形成多个任务并发）
- 主队列（这是一个特殊的串行队列，而且队列中的任务一定会在主线程中执行）

两种执行方式：

- 同步执行
- 异步执行

关于同步异步、串行并行和线程的关系，下面通过一个表格来总结
![](/assets/pic23-1.jpg)

可以看到，同步方法不一定在本线程，异步方法方法也不一定新开线程（考虑主队列）。

- 创建和管理 Dispatch Queue
 dispatch_queue_create 函数用于创建 queue，两个参数分别是 queue 名和一组 queue 属性。调试器和性能工具会显示 queue 的名字，便于你跟踪任务的执行。
**创建queue:**
 ```
  - (void)createQueue {
   //串行队列
    dispatch_queue_t queue = dispatch_queue_create("", NULL);
    dispatch_queue_t serialQueue = dispatch_queue_create("serial_queue", DISPATCH_QUEUE_SERIAL);
    //并行队列
    dispatch_queue_t concurrentQueue = dispatch_queue_create("concurrent_queue", DISPATCH_QUEUE_CONCURRENT);    
}
```

**获得公共 Queue:**
GCD 提供函数，让应用访问几个公共 Dispatch Queue：

- 使用 dispatch_get_main_queue 函数获得应用主线程关联的串行 dispatch queue。
- 使用 dispatch_get_global_queue 来获得共享的并发 queue，优先级从低到高依次是`DISPATCH_QUEUE_PRIORITY_LOW，DISPATCH_QUEUE_PRIORITY_DEFAULT，DISPATCH_QUEUE_PRIORITY_HIGH。`
```
```
