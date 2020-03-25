---
title: IOS 编码风格指南
categories: 编程规范
tags: 编码风格指南
date: 2018-08-21
---
# Objective-C 编码规范   

## <a name='TOC'/></a>目录

- [命名](#naming)
  - [基本原则](#naming-basic-principle)
  - [命名空间](#namespace)
  - [方法名](#naming-method)
  - [协议名](#naming-protocol)
  - [通知命名](#naming-notifications)
  - [临时变量命名](#naming-temporary-variable)
  - [常量命名](#naming-constant)
  - [大小写](#naming-match-case)
  - [缩写](#abbreviation)
  - [其他](#naming-others)
- [代码格式化](#formatting)
  - [空格](#spaces)
  - [花括号](#braces)
  - [折行](#line-wrap)
- [代码组织](#code-organization)
- [类](#class)
  - [Property attributes](#property-attributes)
- [注释](#comment)
  - [块注释](#block-comment)
- [其他](#others)
  - [异常](#exception)
- [参考](#reference)
- [iOS 开发规范&建议](#suggestion)

## <a name='naming'/></a>命名

### <a name='naming-basic-principle'></a>基本原则

- 仿照 Cocoa 风格来，使用长命名风格
- 变量命名推荐的命名语素顺序是：最开头是命名空间简写，然后越重要、区别度越大的语素越要往前放。经典的结构是：作用范围+限定修饰+类型。例：

```C
extern ushort APIDefaultPageSize;        // 还行，能明白意思了
extern ushort APIDefaultFetchPageSize;   // 加上些限定更好一些
extern ushort APIFetchPageSizeDefault;   // 再好些，把重要的往前放

MSToolbarComment    // 不推荐
MSCommentToolbar    // OK，把类型（toolbar）置后
```

### <a name='namespace'></a>命名空间

- 类名、protocols、C 函数、常量、结构体和枚举应带有命名空间前缀；
- 类方法不要带前缀，结构体字段也不要带前缀

### <a name='naming-method'></a>方法名

- 以 `alloc`、`copy`、`init`、`mutableCopy`、`new` 开头的方法要注意，它们会改变 ARC 的行为。[^1]
- 以 `get`、`set` 开头的方法有特殊的意义，不要随意定义。
  1. set 是属性默认的设置方法，如果函数不是为了设置类成员，则不要用 `set` 开头，可用 `setup` 替代。
  2. get 和属性方法无关，但在 Cocoa 中，其标准行为是通过引用传值，而不是直接返回结果的。欲获取变量，直接以变量名为名，如：`userInfomation`，而不是 `getUserInfomation`。

例：

```Objective-C
// OK
- (NSString *)name;

// 糟糕，应用上面的写法
- (NSString *)getName;

// OK，但极少使用
- (void)getName:(NSString **)buffer range:(NSRange)inRange;


// OK
- (NSSize)cellSize;

// 糟糕，应用上面的写法
- (NSSize)calcCellSize;


// 对 controller 做一般设置，OK
- (void)setupController;

// 列出具体设置的内容，更好
- (void)setupControllerObservers;

// 糟糕，set 专用于设置属性
- (void)setController;
```

```Objective-C
// 来自官方文档
insertObject:atIndex:    // OK
insert:at:               // 不清晰，插入了什么？at 具体指哪里？
removeObjectAtIndex:     // OK
removeObject:            // OK
remove:                  // 糟糕，什么被移除了？
```

### <a name='naming-protocol'></a>协议名

好的协议名应能立刻让人分辨出这不是一个类名，除了以常用的 delegate、dateSource 做结尾外，还可以使用 …ing 这种形式，如：`NSCoding`、`NSCopying`、`NSLocking`。

### <a name='naming-notifications'></a>通知命名

基本命名格式是：`[与通知相关的类名] + [Did | Will] + [UniquePartOfName] + Notification`，例：

```Objective-C
NSApplicationDidBecomeActiveNotification
NSWindowDidMiniaturizeNotification
NSTextViewDidChangeSelectionNotification
NSColorPanelColorDidChangeNotification
```

### <a name='naming-temporary-variable'></a>临时变量命名

- 临时变量可以写得很短，如 i、k、vc 这样；
- 临时变量可以使用匈牙利前缀，但数据类型不可以作为前缀：

```C
// OK
wCell, vcMaster, vToolbar

// 糟糕，数据类型作为前缀
bool_switchState, floatBoxHeight
```

推荐的前缀：

| 前缀 | 含义                         |
| ---- | ---------------------------- |
| ix   | 序号，起始为 0               |
| in   | 序号（自然数范围），起始为 1 |
| if   | 类型为浮点的“序号”           |
| x    | 坐标                         |
| y    | 坐标                         |
| w    | 宽度                         |
| h    | 高度                         |
| vc   | 视图控制器                   |
| v    | 视图                         |

### <a name='naming-constant'></a>常量命名

除以上规则约定外，其他常量约定了以下前缀：

| 前缀   | 含义                  |
| ------ | --------------------- |
| k      | 宏常量                |
| CDEN   | Core Data entity name |
| UDk    | User Default key      |
| APIURL | 接口地址              |

另见：[常量管理](#constant)

### <a name='naming-match-case'></a>大小写

- 类名采用大驼峰（`UpperCamelCase`）
- 类成员、方法小驼峰（`lowerCamelCase`）
- 局部变量大小写首选小驼峰，也可使用小写下划线的形式（`snake_case`）
- C 函数的命名用大驼峰

### <a name='abbreviation'/></a>缩写

可以使用广泛使用的缩写，如 `URL`、`JSON`，并且缩写要大写。但像将`download`简写为`dl`这种是不可以的。

```Objective-C
// OK
ID, URL, JSON, WWW

// 糟糕
id, Url, json, www

destinationSelection       // OK
destSel                    // 糟糕
setBackgroundColor:        // OK
setBkgdColor:              // 糟糕
```

### <a name='naming-others'></a>其他

i，j 专用于循环标号

为私有方法命名不要直接以“\_”开头，而应以“命名空间\_”开头。

## <a name='formatting'/></a>代码格式化

### <a name='spaces'></a>空格

类方法声明在方法类型与返回类型之间要有空格。

```Objective-C
// 糟糕
-(void)methodName:(NSString *)string;

// OK
- (void)methodName:(NSString *)string;
```

条件判断的括号内侧不应有空格。

```C
// 糟糕
if ( a < b ) {
    // something
}

// OK
if (a < b) {
    // something
}
```

关系运算符（如 `>=`、`!=`）和逻辑运算符（如 `&&`、`||`）两边要有空格。

```C
// OK
(someValue > 100)? YES : NO
```

二元算数运算符两侧是否加空格不确定，根据情况自己定。一元运算符与操作数之前没有空格。

多个参数逗号后留一个空格（这也符合正常的西文语法）。

### <a name='braces'></a>花括号

方法的花括号推荐在一行。

```Objective-C
  - (void)methodName:(NSString *)string {
    ↑空格                               ↑空格,推荐花括号在一行
      if () {
     空格↑  ↑空格，花括号不要另起一行
      }
      else {
     要换行↑空格，花括号不要另起一行
      }
  }
```

**动机**

> Xcode 默认的花括号位置是这样的：方法内部的各种补全都是在同一行的；方法定义的比较混乱，默认模版另起一行，但从 Interface Builder 中连线生成的方法在同一行的。
>
> 考虑到 Xcode 的默认行为，方法内部要另起一行，方法所在行不强制定死。另外，模版可以定制，而 IB 生成的代码不可定制，所以不另起一行的写法优先。
>
> 另起一行的写法在代码折叠后非常难看。

### <a name='line-wrap'></a>折行

与多数其他规范不同，不建议手动折行。

**动机**

> 手动折行的效果严重宽度依赖于窗口宽度——窗口过宽浪费宝贵的屏幕空间，较窄时可能无法阅读。而且 Xcode 自动折行的效果还是不错的。

## <a name='code-organization'></a>代码组织

- 函数长度（行数）不应超过 2/3 屏幕，禁止超过 70 行。
  : 例外：对于顺序执行的初始化函数，如果其中的过程没有提取为独立方法的必要，则不必限制长度。
- 单个文件方法数不应超过 30 个
- 不要按类别排序（如把 IBAction 放在一块），应按任务把相关的组合在一起
- 禁止出现超过两层循环的代码，用函数或 block 替代。

尽早返回错误：

```Objective-C
// 为了简化示例，没有错误处理，并使用了伪代码

// 糟糕的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    Task *aTask;
    if ([path isURL]) {
        if ([fileManager isWritableFileAtPath:path]) {
            if (![taskManager hasTaskWithPath:path]) {
                aTask = [[Task alloc] initWithPath:path];
            }
            else {
                return nil;
            }
        }
        else {
            return nil;
        }
    }
    else {
        return nil;
    }
    return aTask;
}

// 改写的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    if (![path isURL]) {
        return nil;
    }

    if (![fileManager isWritableFileAtPath:path]) {
        return nil;
    }

    if ([taskManager hasTaskWithPath:path]) {
        return nil;
    }

    Task *aTask = [[Task alloc] initWithPath:path];
    return aTask;
}
```

## <a name='class'/></a>类

禁止在类的 interface 中定义任何 iVar 成员，只允许使用属性，但可以在特定情形中使用属性生成的 iVar。

尽量总是使用点操作符访问属性，而不是属性生成的 iVar 变量。以下情形除外：

- 明确要避免修改产生 KVO 通知的；
- 需重写属性 getter 或 setter 的；
- 性能分析确定使用属性会导致性能不可接受的；
- 多线程环境中，为防止互斥一次进行多个修改的；
- init、dealloc 方法中。

动机

> 如果使用 iVar，很多情况要特殊处理，容易出错。总是使用成员，规则简单，不易出问题。
>
> 直接访问 iVar 的 block 会 retain iVar 所属的对象，这点很容易被忽略
>
> 定义和使用 iVar 都会产生编译警告，只不过默认设置没启用这两个警告

### <a name='property-attributes'></a>Property attributes

什么时候使用 copy？

- block 属性要定义成 copy。
- 当一个属性赋值后不期望改变时应当用 copy，最常见的类型如 NSString、NSURL。可变类型的成员，如 NSMutableArray、NSMutableDictionary 不能定成 copy 的。

相关 Demo 可在 https://github.com/BB9z/PropertyTest 获得。

## <a name='constant'></a>常量

除非调试用的、控制不同编译模式行为的常量可用宏外，其他常量不得用宏定义。

常量定义示例：

```
// 头文件
extern ushort APIFetchPageSizeDefault;    // 无const，可在外部修改

// 实现文件
ushort APIFetchPageSizeDefault = 10;
```

## <a name='comment'></a>注释

尽量让代码可以自表述，而不是依赖注释。

> 注释应该表达那些代码没有表达以及无法表达的东西。如果一段注释被用于解释一些本应该由这段代码自己表达的东西，我们就应该将这段注释看成一个改变代码结构或编码惯例直至代码可以自我表达的信号。我们重命名那些糟糕的方法和类名，而不是去修补。我们选择将长函数中的一些代码段抽取出来形成一些小函数，这些小函数的名字可以表述原代码段的意图，而不是对这些代码段进行注释。尽可能的通过代码进行表达。你通过代码所能表达的和你想要表达的所有事情之间的差额将为注释提供了一个合理的候选使用场合。对那些代码无法表达的东西进行注释，而不要仅简单地注释那些代码没有表达的东西。”[^2]

### <a name='block-comment'></a>块注释

方法内部禁止使用块注释。除非要临时注释大段代码，一般情况总应使用行注释。

**动机**

> 因为块注释不能正确嵌套。

## <a name='others'></a>其他

### <a name='exception'></a>异常

- 作为被调用模块的维护者，当被调用不当时（参数有问题、不和时宜），如何处理需要考虑（抛出异常还是返回错误状态）；
- 不要依赖 try catch，它不是代替你做检查、填补遗漏的工具。

## <a name='reference'></a>参考

- https://github.com/lzyy/iOS-Team-Norms/blob/master/CodeStyle.md
- [Coding Guidelines for Cocoa](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
- https://github.com/github/objective-c-conventions
- https://github.com/jverkoey/iOS-Best-Practices
- [你们是如何为 View Controller 的变量命名的呢？ - V2EX](//www.v2ex.com/t/25732)
- [代码大全(第 2 版) - 亚马逊](http://www.amazon.cn/dp/B0061XKRXA)

<a name='footnote'></a>
[^1]: [再谈 ARC - 苹果核](http://pingguohe.net/2012/06/22/talk_arc_again/)
[^2]: [只对代码无法表达的东西写注释 - Tony Bai](http://bigwhite.blogbus.com/logs/125602412.html)

# <a name='suggestion'></a>iOS 开发规范&建议

1.精简代码, 返回最后一句的值，这个方法有一个优点，所有的变量都在代码块中，也就是只在代码块的区域中有效，这意味着可以减少对其他作用域的命名污染。但缺点是可读性比较差

```
NSURL *url = ({ NSString *urlString = [NSString stringWithFormat:@"%@/%@", baseURLString, endpoint];
    [NSURL URLWithString:urlString];
});
```

2.关于编译器：关闭警告：

```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Warc-performSelector-leaks"
[myObj performSelector:mySelector withObject:name];
#pragma clang diagnostic pop
```

3.忽略没用的变量

```
#pragma unused (foo)
明确定义错误和警告
#error Whoa, buddy, you need to check for zero here!
#warning Dude, don't compare floating point numbers like this!
```

4.避免循环引用

如果【block 内部】使用【外部声明的强引用】访问【对象 A】, 那么【block 内部】会自动产生一个【强引用】指向【对象 A】
如果【block 内部】使用【外部声明的弱引用】访问【对象 A】, 那么【block 内部】会自动产生一个【弱引用】指向【对象 A】

```
__weak typeof(self) weakSelf = self;
dispatch_block_t block = ^{
   	[weakSelf doSomething]; // weakSelf != nil
	// preemption, weakSelf turned nil
	[weakSelf doSomethingElse]; // weakSelf == nil
};
```

最好这样调用：

```
__weak typeof(self) weakSelf = self;
myObj.myBlock = ^{
    __strong typeof(self) strongSelf = weakSelf;
    if (strongSelf) {
        [strongSelf doSomething]; // strongSelf != nil
        // preemption, strongSelf still not nil（抢占的时候，strongSelf 还是非 nil 的)
        [strongSelf doSomethingElse]; // strongSelf != nil
    }
    else { // Probably nothing... return;
    }
};
```

5.宏要写成大写,至少要有大写,全部小写有时候书写不提示参数;

6.建议书写枚举模仿苹果——在列出枚举内容的同时绑定了枚举数据类型 NSUInteger，这样带来的好处是增强的类型检查和更好的代码可读性,示例:

```
// 不推荐写法
typedef enum{
    UIControlStateNormal       = 0,
    UIControlStateHighlighted  = 1 << 0,
    UIControlStateDisabled     = 1 << 1,
} UIControlState;

// 推荐写法
typedef NS_OPTIONS(NSUInteger, UIControlState) {
    UIControlStateNormal       = 0,
    UIControlStateHighlighted  = 1 << 0,
    UIControlStateDisabled     = 1 << 1,
};
```

7.建议加载 xib,xib 名称用 NSStringFromClass(),避免书写错误

```
// 推荐写法
 [self.tableView registerNib:[UINib nibWithNibName:NSStringFromClass([DXRecommendTagVCell class]) bundle:nil] forCellReuseIdentifier:ID];

// 不推荐写法
 [self.tableView registerNib:[UINib nibWithNibName:@"DXRecommendTagVCell" bundle:nil] forCellReuseIdentifier:ID];
```

8.场景需求:在继承中,凡是要求子类重写父类的方法必须先调用父类的这个方法进行初始化操作;建议:父类的方法名后面加上 NS_REQUIRES_SUPER; 子类重写这个方法就会自动警告提示要调用这个 super 方法,示例代码

```
// 注意:父类中的方法加`NS_REQUIRES_SUPER`,子类重写才有警告提示
- (void)prepare NS_REQUIRES_SUPER;
```

9.建议书写属性名不要和系统一样,避免发生莫名其妙的问题;特别注意的是 label;属性名不要写成 textLabel

10.项目中添加 plist 类型文件,不要命名为 info.plist,以防止和系统自带的文件重名,发生莫名其妙的问题;

11.如果控制器已经加载过,就不用再次加载,优化性能

```
if (vc.isViewLoaded) return;
```

12.id 类型属性不能用点语法,调用 get 方法只能用中括号调用,[id 方法名],利用 iOS9 新特性泛型就可以; 比如数组;

```
@property (nonatomic,strong) NSMutableArray *topicsM;
```

13.如果不是属性,尽量不要点语法;使用点语法会让代码简洁。但对于其他情况，都应该使用方括号语法。

```
 //建议
 NSInteger arrayCount = [self.array count];
 view.backgroundColor = [UIColor orangeColor];
 [UIApplication sharedApplication].delegate;

 //不建议
 NSInteger arrayCount = self.array.count;
 [view setBackgroundColor:[UIColor orangeColor]];
 UIApplication.sharedApplication.delegate;
```

14.使用第三方框架,尽量不要更改内部文件,而应该再次封装,个性定制;

15.判断 if 书写方式

建议这样写

```
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    if (indexPath.row == 0) return 44;
    if (indexPath.row == 1) return 80;
    if (indexPath.row == 2) return 50;
    return 44;
}
```

而不是

```
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    if (indexPath.row == 0) {
        return 44;
    }else if (indexPath.row == 1){
        return 80;
    }else if (indexPath.row == 2){
        return 50;
    }else{
        return 44;
    }
}
```

16.接手一个新项目,快速的调试,查看某个模块或者方法的作用,需要注释掉一个方法,或者某个代码块,直接写 return;而不是全选,注释掉;

比如:查看这个方法 loadNewRecommendTags 作用

```
- (void)loadNewRecommendTags
{
    return;

    [SVProgressHUD show];
    // 取消之前的任务
    [self.manager.tasks makeObjectsPerformSelector:@selector(cancel)];
    NSMutableDictionary *params = [NSMutableDictionary dictionary];

    params[@"a"] = @"tag_recommend";
    params[@"c"] = @"topic";
    params[@"action"] = @"sub";
    [self.manager GET:DXCommonUrlPath parameters:params success:^(NSURLSessionDataTask * _Nonnull task, id  _Nonnull responseObject) {

        self.recommendTag = [DXRecommendTag mj_objectArrayWithKeyValuesArray:responseObject];
        [self.tableView reloadData];
        [SVProgressHUD dismiss];
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        DXLog(@"%@",error);
        [SVProgressHUD dismiss];
    }];
}
```

17.在一个自定义的 View 中,或者自定义 cell 中,modal 出一个控制器建议:

```
[UIApplication sharedApplication].keyWindow.rootViewController
```

代替

```
self.window.rootViewController,因为程序可能不止一个window,self.window可能不是主窗口;
```

18.建议:用 CGSizeZero 代替 CGSizeMake(0,0);

CGRectZero 代替 CGRectMake(0, 0, 0, 0);

CGPointZero 代替 CGPointMake(0, 0)

19.监听键盘的通知建议:

```
UIKIT_EXTERN NSString *const UIKeyboardWillChangeFrameNotification
```

而不是,下面代码;因为键盘可能因为改变输入法,切换成表情输入,切换成英文,那么 frame 可能会变高,变矮,不一定会发出下面这些通知,但是肯定会发上面的通知

```
UIKIT_EXTERN?NSString *const UIKeyboardWillShowNotification;
UIKIT_EXTERN?NSString *const UIKeyboardDidShowNotification;
UIKIT_EXTERN?NSString *const UIKeyboardWillHideNotification;
UIKIT_EXTERN?NSString *const UIKeyboardDidHideNotification;
```

20.发布通知的字符串常量规范,建议模仿苹果;如上键盘的通知的书写,加上 const 保证字符串不可更改,以 Notification 结尾,一看就知道是通知;应尽量保证可读性,不要怕句子太长;

```
NSString *const buttonDidClickNotification = @"buttonDidClickNotification";
```

21.如果除数为 0,iOS8 以下会直接报错,(NaN—>Not a Number)iOS9 不会,所以应该判断,比如服务器返回图片的宽高,按比例缩放:

```
CGFloat contentH = textW * self.height / self.width;
```

22.如果声明的属性,只想使用的 get 方法,不使用 set 方法,并且不想让外界更改这个属性的值,那么建议在括号里面加 readonly;示例:

```
@property(nonatomic,readonly,getter=isKeyWindow) BOOL keyWindow;
```

23.如果属性是 BOOL 类型,建议在括号中重写 get 方法名称,以提高可读性,示例代码如上;

24.从系统相册中取照片之前,应该判断系统相册是否可用,如果从相机中拍照获取,要判断相机是否可用

```
// 判断相册是否可以打开
if (![UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypePhotoLibrary]) return;

// 判断相机是否可以打开
if (![UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypeCamera]) return;
```

25.在导航控制中,或它的子控制器,设置导航栏的标题应该用

```
self.navigationItem.title = @“标题”
```

而不建议

```
self.title = @“标题”;
```

26.给 cell 设置分割线,建议用 setFrame:通过设置它高度,设置分割线,而不推荐用给 cell 底部添加一个高度的 UIView,这样做增加了一个控件,从性能上来讲,不如用 setFrame 设置高度

27.大量操作图层会可能造成应用很卡,给用户体验差,所以尽量不要操作图层;比如设置按钮圆角,比如给 button 设置圆角;

```
self.loginBtn.layer.cornerRadius = 5;
self.loginBtn.layer.masksToBounds = YES;
```

28.给分类扩充方法,建议加上前缀,比如第三方框架 SDWebImage,这样做跟系统的方法很容易区分开,减少了程序员之间的沟通成本,同理跟分类添加属性(利用运行时),建议加前缀,以防止苹果官方过一段时间添加了一模一样的属性名,比如给 UITextField 分类添加了 placeholderColor 这个属性,万一某天官方给 placeholder 扩充了这个命名一模一样的属性,那么就不好了

29.凡是在 storyboard 或者 xib 中给某个控件添加颜色,颜色对角线有分割线,表示可以设置透明度,如果给这个控件设置透明度建议在这里设置,而不是设置 alpha,因为设置了 alpha,那么上面有文字也会随着透明度变大,而变得不清楚;可以设置 background -->other -->opacity

30.整形转化成浮点型,不建议这么写 a / b _ 1.0,这样写是错误写法,示例 1 / 2 _ 1.0;根据运算法则,从左到右,0 _ 1.0 == 0,而应该在前面写 1.0 _ 1 / 2;建议直接强转 (double)a/b;

31.抽取方法,或者写工具类,能写类方法,尽量写成类方法,减少了创建对象的步骤,比如给 UIView 扩充分类加载 xib,viewWithXib;

32.耗时操作应该放在子线程,避免卡主主线程,比如计算文件大小,下载大文件,清除缓存;

33.声明一个属性,如果是对象,比如数组,不能以 new 单词开始,否则直接报错,因为 new 在 OC 中是生成一个对象的方法,有特殊含义;比如,

```
@property (nonatomic,strong) NSMutableArray *newTopicsM;
注意:如果newtopicsM是一个单词(区别于驼峰标志),这样写不会报错;如果是基本数据类型则不会报错,比如

@property (nonatomic,assign) int newNumber;
但是如果一定要写new单词开头的属性,那么声明属性的时候,重写getter方法名称只不过使用getter方法的时候注意下
```

34.在自定义方法中,and 这个词的用法应该保留。它不应该用于多个参数来说明，就像 initWithWidth:height 以下这个例子：

```
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

而不应该

```
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
```

35.多线程同步问题造成的 Crash
对于数据源或 model 类一定要注意多线程同时访问的情况, 可以用 GCD 的串行队列来同步线程.

36.Observer 的移除
现在的代码里面很多需要用到 Observer, 根据被观察对象的状态来相应的 Update UI 或者执行某个操作. 注册 observer 很简单, 但是移除的时候就出问题了, 要么是忘记移除 observer 了, 要么是移除的时机不对. 如果某个被观察对象已经被释放了, observer 还在, 那结果只能是 crash 了, 所以切记至少在 dealloc 里面移除一下 observer

37.NSArray, NSDictionary 成员的判空保护
在 addObject 或 insertObject 到 NSArray 或者 NSDictionary 时最好加一下判空保护, 尤其是网络相关的逻辑, 如果网络返回为空(jason 解析出来为空), 需要先判空再 add 到 array 里面

38.commit 代码之前一定要保证没有 warning, 没有内存泄露, 确保都 OK 之后再上传代码。上传代码之前 Command + Shift + B 静态分析一下, 看看有没有什么 issue 即可
