在进行ios开发前，我们很有必要了解一个app的生命周期，从生到死，我们参与了哪些。

**1.启动流程**
下面给出苹果官方的流程图
![](/assets/pic5-1.png)

可以看到我们熟悉的main方法，也就程序的入口点。在main方法中调用了UIApplicationMain方法，原型为：
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

```
int main(int argc, char* argv[])
{
    @autoreleasepool {
        int retVal = UIApplicationMain(argc, argv, nil, @"AppDelegate");
        return retVal;
    }
}
```
**UIApplication的作用：**
每一个app事实上就一个UIApplication实例，通过sharedApplication方法拿到这个实例
```
    [UIApplication sharedApplication];
```
