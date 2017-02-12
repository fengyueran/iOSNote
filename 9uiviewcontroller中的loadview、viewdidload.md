#UIViewController中的loadView、viewDidLoad、viewDidUnload

我们创建的controller中默认有viewDidload方法，在创建自定义UI时也总是在这个方法中写，why?与其相关的loadView以及viewDidUnload方法又是在什么时候调用？

- loadView

 通过调用栈及私有方法重写可以了解loadView方法的调用过程大致是：[UIViewController View]->[UIViewController loadViewIfRequired]->[UIViewController loadView];
<div align="center">
<img src = "assets/pic9-1.png" width="400" height="360"</>
</div>


- viewDidLoad

 viewDidLoad什么时候会被调用呢？见名思意，view在已经载入的时候调用，来看官方文档的解释：

 <table><tr><td bgcolor=#7FFFD4>Called after the controller's view is loaded into memory.
This method is called after the view controller has loaded its view hierarchy into memory. This method is called regardless of whether the view hierarchy was loaded from a nib file or created programmatically in the loadView method. You usually override this method to perform additional initialization on views that were loaded from nib files.</td></tr></table>

 即viewDidLoad方法在view加载到内存中后就会调用，一般情况下该方法只会调用一次，但是当出现内存警告，view被unload从内存中清除后会重新调用该方法，或者在不断的创建controller,然后push it again and again。