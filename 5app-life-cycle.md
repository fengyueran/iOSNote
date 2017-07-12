在进行ios开发前，我们很有必要了解一个app的生命周期，从生到死，我们参与了哪些。

**1.程序入口**

app启动同样由我们熟悉的main方法开始。
```
int main(int argc, char* argv[])
{
    @autoreleasepool {
        int retVal = UIApplicationMain(argc, argv, nil, @"AppDelegate");
        return retVal;
    }
}
```
在main方法中调用了UIApplicationMain方法，原型为：
```
int UIApplicationMain(int argc, char * _Nonnull *argv, NSString *principalClassName, NSString *delegateClassName);
```
**参数说明：**
argc:命令行输入参数的个数
argv：参数数组
principalClassName：UIApplication
类或子类的名称，如果为nil则为UIApplication
delegateClassName：代理类名

该方法的主要工作有：
- 创建UIApplication单例对象
- 为单例对象设置代理并初始化
- 创建main event loop
- 选择app info.plist中确定的main nib file载入，没有则不载入


**UIApplication的作用：**
每一个app事实上就一个UIApplication实例，通过sharedApplication方法拿到这个实例
```
    [UIApplication sharedApplication];
```
他的主要工作就是处理用户事件，他会启动一个事件队列用于管理事件，当收到事件后会发送当前事件到合适的目标控件进行处理，UIApplication还维护一个UIWindow列表，通过该列表就可以与任何一个UIView对象接触。

**AppDelegate的作用：**
处理app运行中重要的runtime事件，如低内存警告、程序启动退出等。

**2.启动流程**
下面给出苹果官方的流程图
![](/assets/pic5-1.png)

