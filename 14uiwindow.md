###UIWindow
UIWindow继承自UIView，也就是说UIWindow是一个视图容器。

**1.UIWindow简介UIWindow简介**

UIWindowUIWindow特性：
- 它是最底层的视图容器，所有可见的视图都是依附在它之上。没有它就没有可见视图。
- 传递触摸和键盘等事件给视图
- 每一个window是独立的
- 同一时间只有一个window可以成为key window
- 只有key window 才能接收键盘或非触摸类事件

那么UIWindow是如何渲染到屏幕呢？
- UIScreen对象识别物理屏幕连接到设备
- UIWindow对象提供绘画支持给屏幕
- UIView执行绘画，当窗口要显示内容的时候，UIView绘画出他们的内容并附加到窗口上。

**2.UIWindow的创建**

1）UIWindow是什么时候创建的?

在App life cycle一章中我们了解到，UIApplication会载入app info.plist中确定的main nib file，如果我们将info.plist中Main storyboard file base name这行键值对删掉，程序启动就成黑屏了。此时view没有显示，由此猜测UIWindow实在载入storybord时创建。

那么系统是如何加载storyboard的呢？
由苹果[官方文档][1]可知主要有三个流程
- 实例化UIWindow
- 加载storyboard并实例化它的view controller
- 将新的view controller 设定为window rootViewController并使窗口显示在屏幕上。

由此可知info.plist里没有指定main就不会加载storyboard，也就不会创建window, 这时我们可以手动创建window。

2）UIWindow如何创建?

显然参照系统创建的过程就可以完成UIWindow的创建，在didFinishLaunchingWithOptions中
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    //创建window
    self.window = [[UIWindow alloc]initWithFrame:[UIScreen mainScreen].bounds];
    //创建窗口的根控制器
    UIViewController *rootVc = [[UIViewController alloc]init];
    self.window.rootViewController = rootVc;
    //显示窗口
    [self.window makeKeyAndVisible];
    return YES;
}
```
窗口显示需要注意的地方:

- 当发生屏幕旋转事件的时候，UIapplication对象会将旋转事件传递给UIWindow，UIWindow又会将旋转事件传递给它的根控制器，由根控制器决定是否需要旋转。UIapplication对象 -> UIWindow -> 根控制器。
（[self.window  addsubview:rootVc.view];没有设置根控制器，所以不能跟着旋转）。
- 设置根控制器可以将对应界面的事情交给对应的控制器去管理。

makeKeyAndVisible的作用：
见名思义，即：
- 将window设置为 keywindow
- 将window移动到其他window的前面并显示

怎么证明呢？运行下面的代码
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    self.window = [[UIWindow alloc]initWithFrame:[UIScreen mainScreen].bounds];

    UIViewController *rootVc = [[UIViewController alloc]init];
    self.window.rootViewController = rootVc;
    
    NSLog(@"window=%@",self.window);
    NSLog(@"keyWindow=%@",application.keyWindow);
    [self.window makeKeyAndVisible];
    NSLog(@"window=%@",self.window);
    NSLog(@"keyWindow=%@",application.keyWindow);
    
    return YES;
}
```
打印内容：
```
window=<UIWindow: 0x7f8fb7502b80; frame = (0 0; 375 667); hidden = YES; 
keyWindow=(null)

window=<UIWindow: 0x7f8fb7502b80; frame = (0 0; 375 667); 
keyWindow=<UIWindow: 0x7f8fb7502b80; frame = (0 0; 375 667); 
```



[1]:https://developer.apple.com/library/content/documentation/WindowsViews/Conceptual/WindowAndScreenGuide/WindowScreenRolesinApp/WindowScreenRolesinApp.html