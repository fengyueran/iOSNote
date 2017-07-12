###UIWindow
UIWindow继承自UIView，也就是说UIWindow是一个视图容器。

UIWindow特性：
- 它是最底层的视图容器，所有可见的视图都是依附在它之上。没有它就没有可见视图。
- 传递触摸和键盘等事件给视图
- 每一个window是独立的
- 同一时间只有一个window可以成为key window
- 只有key window 才能接收键盘或非触摸类事件