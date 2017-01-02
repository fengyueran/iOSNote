# First Chapter


```objc
作为iOS的初学者，在使用一些基本控件的时候总是遇到一些坑，
每次google再google,基本能解决问题，但不免浪费了时间。于是想把控件的常规用法以及需要特别注意的地方记录下来，如有不对之处，还请指正。
```
##UIView
```objc
1. UIView可称之为控件或视图，是所有控件的父控件。
2. 一个app所呈现出来的，看得到的，如图片、文字等都是来自于UIView。
!(/assets/pic2.png)
3. 不难想象，一个视图要呈现出来，必须有位置和大小等属性，所以View提供了各个控件所需的最基本属性和方法。
```
###UIView基本用法
- 设置frame属性(视图呈现的最基本元素),由位置和大小构成，OC抽象为CGRect这个结构。

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

UIView通过CGRectMake方法返回一个CGRect,即一个View的坐标原点和大小。在UIKit中，坐标系的原点(0，0)在左上角，x值向右正向延伸，y值向下正向延伸，如下图。
一个view的frame是相对其父类而言的，即view的坐标原点是相对父类view偏离的位置。
































