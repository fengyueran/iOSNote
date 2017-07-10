 ###iOS 并发编程 4 种方式
 
 - Pthreads
 - NSThread
 - GCD
 - Operation && Operation Queues
 
**1) Pthreads**
>POSIX线程（POSIX threads），简称Pthreads，是线程的POSIX标准。该标准定义了创建和操纵线程的一整套API。在类Unix操作系统（Unix、Linux、Mac OS X等）中，都使用Pthreads作为操作系统的线程。
跨平台，适用于多种操作系统，可移植性强。

是一套纯C语言的通用API，且线程的生命周期需要程序员自己管理，使用难度较大，笔者不建议使用。

下面简单给出使用 Pthreads 并发编程的范例代码和 Pthreads 相关数据结构：
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