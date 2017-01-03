# First Chapter


作为iOS的初学者，在使用一些基本控件的时候总是遇到一些坑，每次google再google,基本能解决问题，但不免浪费了时间。于是想把控件的常规用法以及需要特别注意的地方记录下来，如有不对之处，还请指正。

##UIView

- UIView可称之为控件或视图，是所有控件的父控件。
- 一个app所呈现出来的，看得到的，如图片、文字等都是来自于UIView,如下图所示。
<div align="center">
<img src = "assets/pic1.png" width="400" height="360"</>
</div>

- 不难想象，一个视图要呈现出来，必须有位置和大小等属性，所以View提供了各个控件所需的最基本属性和方法。

###UIView属性
- frame属性

frame属性为视图呈现的最基本元素,由位置和大小构成，OC抽象为CGRect这个结构。

```objc
struct CGRect {
    CGPoint origin;
    CGSize size;
};
typedef struct CGRect CGRect;
```
frame属性的基本设置方法：
```objc
UIView *view = [UIView alloc]init];
view.frame = CGRectMake(CGFloat x, CGFloat y, CGFloat width, CGFloat height);

```
我们看到这里用了alloc，再init的方法，即分配内存进而初始化对象，那什么不直接用new呢？从new方法的原型可以看到alloc的方法和new方法几乎没有区别。在原始的OC方法中创建对象一般也是用new方法,在引入Cocoa等框架后，设计者逐渐明白了不能在一个树上吊死的理念，才逐步将分配内存和初始化对象分开来，使得初始化有了更多的选择，如常用的initWith方法。
```objc
+ (id) new
{
    return [[self alloc] init];
}
```
差点跑偏，说好的View呢!

UIView通过CGRectMake方法返回一个CGRect,即一个View的坐标原点和大小。在UIKit中，坐标系的原点(0，0)在左上角，x值向右正向延伸，y值向下正向延伸，如下图所示。 
 <div align="center">
      <img  src = "assets/pic2.png"</>
 </div>


一个view的frame是相对其父类而言的，即view的坐标原点是相对父类view偏离的位置。

- bounds属性

bounds属性和frame属性类似,其主要区别是坐标系的不同，frame以其父类左上角为原点O(0, 0)，如上图ViewA以父类O(0,0)为原点的o'（x,y)。bounds属性以自己为原点，位置坐标为（0，0）。

```objc
frame = a view's location and size using the parent view's coordinate system
  Important for: placing the view in the parent
bounds = a view's location and size using its own coordinate system
  Important for: placing the view's content or subviews within itself
```
- backgroundColor属性
设置背景颜色方法：
点语法或set方法,如果希望uivew透明，可以设置其背景色为clearColor，即白色透明度为0的背景色。
```objc
view.backgroundColor = [UIColor redColor];
or [view setBackgroundColor:[UIColor redColor]];
or view.backgroundColor = [UIColor colorWithRed:0.1 green:0.1 blue:0.5 alpha:1];
```
- alpha属性

设置透明度方法：
透明度范围为0-1，0为全透明，1为不透明。

```objc
view.alpha = 0.5;

```

- tintColor属性

tintColor在ios7新加入，有点魔法色的意思，可以重新渲染图片的色彩，默认为nil。如果一个view没有显示的指定tintColor，那它会继承父类的tintColor,通过设置keywindow的tintColor就可以设置整个app的主题色。

 
```objc

[[UIApplication sharedApplication] keyWindow].tintColor = [UIColor redColor]; 
``` 
###UIView常见用法


































