#UIViewController中的loadView、viewDidLoad、viewDidUnload

我们创建的controller中默认有viewDidload方法，在创建自定义UI时也总是在这个方法中写，why?与其相关的loadView以及viewDidUnload方法又是在什么时候调用？

- viewDidLoad
viewDidLoad什么时候会被调用呢？见名思意，view在已经载入的时候调用，来看官方文档的解释：

 <table><tr><td bgcolor=#7FFFD4>Called after the controller's view is loaded into memory.
This method is called after the view controller has loaded its view hierarchy into memory. This method is called regardless of whether the view hierarchy was loaded from a nib file or created programmatically in the loadView method. You usually override this method to perform additional initialization on views that were loaded from nib files.</td></tr></table>