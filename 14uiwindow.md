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

1.UIWindow是什么时候创建的?