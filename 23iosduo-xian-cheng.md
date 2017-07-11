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

 比如dispatch_sync，这个函数会把一个block加入到指定的队列中，而且会一直等到执行完blcok，这个函数才返回。因此在block执行完之前，调用dispatch_sync方法的线程是阻塞的。
- 异步执行

 一般使用dispatch_async，这个函数也会把一个block加入到指定的队列中，但是和同步执行不同的是，这个函数把block加入队列后不等block的执行就立刻返回了。

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
- 使用 dispatch_get_global_queue 来获得共享的并发 queue，这里的两个参数得说明一下：第一个参数用于指定优先级,优先级从低到高依次是`DISPATCH_QUEUE_PRIORITY_LOW，DISPATCH_QUEUE_PRIORITY_DEFAULT，DISPATCH_QUEUE_PRIORITY_HIGH。`第二个参数目前未使用到，默认0即可 

```
//获得共享并发queue
- (void)getQueue {
    //获取主队列
    dispatch_queue_t queue = dispatch_get_main_queue();
    //获取全局队列
    dispatch_queue_t globalQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
}
```


**添加任务到queue:**
要执行一个任务，你需要将它 dispatch 到一个适当的 dispatch queue，你可以添加同步或异步任务，也可以单个或按组（group）来 dispatch。
**
dispatch_async && dispatch_sync:**
```
        //添加同步任务到global queue
dispatch_sync(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        NSLog(@"sync task");
    });
    //添异步任务到global queue   dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSLog(@"async task");

    });
```
**死锁：**
如下代码，在主队列中dispatch_sync添加一个同步任务(block)到主队列，block要在**当前队列**执行就需要等待主队列中dispatch_sync这个任务结束，而执行完block dispatch_sync这个任务才算结束，两个任务相互等待造成无法执行block，造成死锁。
```
-(void)viewDidLoad{
    [super viewDidLoad];
    //添加同步任务到main queue
  dispatch_sync(dispatch_get_main_queue(), ^{
        NSLog(@"main task");
    });
}
```

使用 Dispatch Queue 实现应用并发时，也需要注意线程安全性：

- Dispatch queue 本身是线程安全的。换句话说，你可以在应用的任意线程中提交任务到 dispatch queue，不需要使用锁或其它同步机制。
- 不要在执行任务代码中调用 dispatch_sync 函数调度相同的 queue，这样做会死锁这个 queue。如果你需要 dispatch 到当前 queue，需要使用 dispatch_async 函数异步调度。


**Dispatch Group:**

用于监控一组 Block 对象完成(你可以同步或异步地监控 block)。
```
       dispatch_group_t group = dispatch_group_create();
        __block UIImage *image1 = nil;
        __block UIImage *image2 = nil;
        
        dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
            image1 = [self loadImageD:imgUrl1];
        });
        
        dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
            image2 = [self loadImageD:imgUrl2];
        });
        //dispatch_group 执行完一组异步操作后可以通过 dispatch_group_notify来通知主线程，反馈信息给用户。
        dispatch_group_notify(group, dispatch_get_main_queue(), ^{
            self.imageView1.image = image1;
            self.imageView2.image = image2;
        });
```

**dispatch_after：方法**
通过 GCD 还可以进行简单的定时操作，比如在 2 秒后执行某个 block 。代码如下：
```
    NSLog(@"Delay 2 seconds");
    double delayInSeconds = 2.0;
    dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, delayInSeconds * NSEC_PER_SEC);
    dispatch_after(time, dispatch_get_main_queue(), ^{
         [self loadImageSource:imgUrl1];
    });
```
**线程安全：**
把写操作与读操作都安排在同一个同步串行队列里面执行，这样的话，所有针对属性的访问操作就都同步了。它只可以实现单读、单写。整体来看，我们最终要解决的问题是，在写的过程中不能被读，以免数据不对，但是读与读之间并没有任何的冲突！

```
@interface ZYPerson ()
@end
 
static NSString *_name;
static dispatch_queue_t _concurrentQueue;
@implementation ZYPerson
- (instancetype)init
{
    if (self = [super init]) {
       _concurrentQueue = dispatch_queue_create("com.person.syncQueue", DISPATCH_QUEUE_CONCURRENT);
    }
    return self;
}
- (void)setName:(NSString *)name
{
    dispatch_barrier_async(_concurrentQueue, ^{
        _name = [name copy];
    });
}
- (NSString *)name
{
    __block NSString *tempName;
    dispatch_sync(_concurrentQueue, ^{
        tempName = _name;
    });
    return tempName;
}
@end
```
```
#import <Foundation/Foundation.h>
 
@interface ZYPerson : NSObject
@property (nonatomic, copy) NSString *name;
@end
 
 
#import "ZYPerson.h"
 
@interface ZYPerson ()
@end
 
static NSString *_name;
static dispatch_queue_t _concurrentQueue;
@implementation ZYPerson
- (instancetype)init
{
    if (self = [super init]) {
       _concurrentQueue = dispatch_queue_create("com.person.syncQueue", DISPATCH_QUEUE_CONCURRENT);
    }
    return self;
}
- (void)setName:(NSString *)name
{
    dispatch_barrier_async(_concurrentQueue, ^{
        _name = [name copy];
    });
}
- (NSString *)name
{
    __block NSString *tempName;
    dispatch_sync(_concurrentQueue, ^{
        tempName = _name;
    });
    return tempName;
}

```
在这个代码中用了dispatch_barrier_async，可以翻译成栅栏（barrier），它可以往队列里面发送任务（块，也就是block），这个任务有栅栏（barrier）的作用。

在队列中，barrier块必须单独执行，不能与其他block并行。这只对并发队列有意义，并发队列如果发现接下来要执行的block是个barrier block，那么就一直要等到当前所有并发的block都执行完毕，才会单独执行这个barrier block代码块，等到这个barrier block执行完毕，再继续正常处理其他并发block。在上面的代码中，setter方法中使用了barrier block以后，对象的读取操作依然是可以并发执行的，但是写入操作就必须单独执行了。


**
4）NSOperation**
NSOperation 和 NSOperationQueue 主要涉及这几个方面：

- NSOperation 和 NSOperationQueue 用法介绍
- NSOperation 的暂停、恢复和取消
- 通过 KVO 对 NSOperation 的状态进行检测
- 多个 NSOperation 的之间的依赖关系
从简单意义上来说，NSOperation 是对 GCD 中的 block 进行的封装，它也表示一个要被执行的任务。

与 GCD 中的 block 类似，NSOperation 对象有一个 start() 方法表示开始执行这个任务。

不仅如此，NSOperation 表示的任务还可以被取消。它还有三种状态 isExecuted、isFinished 和 isCancelled 以方便我们通过 KVC 对它的状态进行监听。
NSOperation的简介：
- 是OC语言中基于GCD的面向对象的封装，使用起来比GCD更加简单。提供了一些GCD不好实现的功能，苹果推荐使用。NSOperation还不用关心线程和线程的声明周期。
- NSOperation是个抽象类无法直接使用。因为方法只有声明没有实现。
- 子类：NSInvocationOperation和NSBlockOperation，自定义NSOperation操作默是异步的。
- 队列 : NSOperationQueue队列默认是并发的。
- 核心：GCD的核心 : 将任务添加到队列中。OP的核心 : 将操作添加到队列中。


**1）使用子类NSInvocationOperation**
```
    NSInvocationOperation *invocationOperation = [[NSInvocationOperation alloc]initWithTarget:self selector:@selector(loadImageSource:)  object:imgUrl];
//  [invocationOperation start];//直接会在当前线程主线程执行
    NSOperationQueue *queue = [[NSOperationQueue alloc]init];
    [queue addOperation:invocationOperation];
```


**2）使用子类NSBlockOperation**
```
    NSBlockOperation *blockOperation = [NSBlockOperation blockOperationWithBlock:^{
        [self loadImageSource:imgUrl];
    }];
    NSOperationQueue *queue = [[NSOperationQueue alloc]init];
    [queue addOperation:blockOperation];
```
**
3) 使用继承NSOperation**

