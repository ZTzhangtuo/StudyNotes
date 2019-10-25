# flutter

## 实例 ： 计数器

```dart
import 'package:flutter/material.dart';
//此行代码作用是导入了Material UI组件库。Material是一种标准的移动端和web端的视觉设计语言， Flutter默认提供了一套丰富的Material风格的UI组件。

//定义一个Main方法。Main方法中用简单RunApp就可以执行我们定义的Widget
void main() => runApp(new MyApp());
/*
Flutter写成的都是UI主键。主要分 StatelessWidget 和 StatefulWidget
整体的入口可以写成 StatelessWidget
*/
class MyApp extends StatelessWidget {
  //这个Widget就是我们App的最基层的Widget了。
  //传入BuildContext给我们使用。
  @override
  Widget build(BuildContext context) {
//这样就可以直接使用Flutter为我们封装好的MaterialApp这个主题的了.从源码可以看到这个是个StatefulWidget
    //[home], [routes], [onGenerateRoute], or [builder] 这个主题下的这些方法不能都为空！！
    return new MaterialApp(

      title: 'Flutter Demo',
      //定义主题
      theme: new ThemeData(
        //主题颜色
        primarySwatch: Colors.blue,
      ),
      home: new MyHomePage(title: '首页面'),
    );
  }
}
//---------------------------------------------------------------------------------
//statefulWidget ，因为Widget都是无状态的，所以如果需要有状态的话，`state`这个类来进行维持
class MyHomePage extends StatefulWidget {
  // 无论你接收不接收数据，MyHomePage({Key key}) : super(key : key);固定死的
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  @override
  _MyHomePageState createState() => new _MyHomePageState();
}
//---------------------------------------------------------------------------------
//flutter中状态的持有类
class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    //这点和小程序很类似。调用setState进行状态同步和刷新。如果不调用这个方法，只是改变值，界面不会发生变化
    setState(() {
      _counter++;
    });
  }
  //state中的`build`方法，会自动在`setState`方法后调用。
  @override
  Widget build(BuildContext context) {
  //Scaffold可以理解程相当于一个html 它的body就是主要的内容。 
    //同时它其实是满足MD的。所以它还能提供对应的组件
    //比如 AppBar .Drawer floatingButton等等
    return new Scaffold(
      appBar: new AppBar(
        //这里，我们从由App.build方法创建的my主页对象中获取值，并使用它设置appbar标题
        title: new Text(widget.title),
      ),
      //这里可以初步的看到，如果想要布局居中显示，可以先包裹一层`Center`
      body: new Center(
       //因为是竖直的布局，所以再次包裹一层Column
        child: new Column(
          //使用这个属性，让元素在垂直线上居中竖直排列
          mainAxisAlignment: MainAxisAlignment.center,
         //children中传入其他组件
          children: <Widget>[
            new Text(
              '点了一次又一次：',
            ),
            new Text(
              '$_counter',
              style: Theme.of(context).textTheme.display1,
            ),
          ],
        ),
      ),
      floatingActionButton: new FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: new Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}

```



## 一、



### 1、StatelessWidget类

不需要可变状态的小部件。

无状态小部件是一个小部件，它通过构建一系列其他小部件来更加具体地描述用户界面，从而描述用户界面的一部分。构建过程以递归方式继续进行，直到用户界面的描述完全具体（例如，完全由 RenderObjectWidget 组成，它描述具体的 RenderObject。

当您描述的用户界面部分不依赖于对象本身中的配置信息和其中构件被夸大的 BuildContext 时，无状态小部件很有用。对于可以动态改变的组合，例如由于具有内部时钟驱动状态，或取决于某些系统状态，请考虑使用 StatefulWidget 。

无状态小部件的构建方法通常只在以下三种情况下调用：第一次将小部件插入树中，第一次在小部件的父级更改其配置时以及第二次使用 InheritedWidget 时，它依赖于更改。



### 2、StatefulWidget类

具有可变状态( state)的Widget(窗口小部件).

状态( state) 是可以在构建Widget时同步读取时 和  在Widget的生命周期期间可能改变的信息

Widget实现者的责任就是 在状态改变时通过  State.setState. 立即通知状态

当您描述的用户界面部分不依赖于对象本身中的配置信息和其中构件被夸大的 BuildContext 时，无状态小部件很有用。对于可以动态改变的组合，例如由于具有内部时钟驱动状态，或取决于某些系统状态，请考虑使用StatefulWidget 。

StatefulWidget 实例本身是不可变的，并将其可变状态存储在由 createState 方法创建的独立状态对象中 ，或者存储在该状态订阅的对象中，例如 Stream 或 ChangeNotifier 对象，其引用存储在 StatefulWidget 的最终字段中本身。

该框架只要调用一个 StatefulWidget 就 调用 createState，这意味着如果该小部件已经插入到多个位置的树中，那么多个 State 对象可能与同一个 StatefulWidget 关联。同样，如果 StatefulWidget 从树中移除，后来在树再次插入时，框架将调用 createState 再创建一个新的目标，简化的生命周期状态的对象。

Stateful widget至少由两个类组成：

- 一个`StatefulWidget`类。
- 一个 `State`类； `StatefulWidget`类本身是不变的，但是`State`类中持有的状态在widget生命周期中可能会发生变化。如果需要主动改变State的状态，需要通过setState()方法进行触发，单纯改变数据是不会引发UI改变的。

`_MyHomePageState`类是`MyHomePage`类对应的状态类。看到这里，读者可能已经发现：和`MyApp` 类不同， `MyHomePage`类中并没有`build`方法，取而代之的是，`build`方法被挪到了`_MyHomePageState`方法中。



### 3、widget

- 在Flutter中，大多数东西都是widget（后同“组件”或“部件”），包括对齐(alignment)、填充(padding)和布局(layout)等，它们都是以widget的形式提供，使用时，需要new ,但也可以省略
- Flutter在构建页面时，会调用组件的`build`方法，widget的主要工作是提供一个build()方法来描述如何构建UI界面（通常是通过组合、拼装其它基础widget）。





### ８、简单快捷键

**`stl`**: StatelessWidget

```dart
class  extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      
    );
  }
}
```

**`stf`**：StatefulWidget

```dart
class  extends StatefulWidget {
  @override
  _State createState() => _State();
}

class _State extends State<> {
  @override
  Widget build(BuildContext context) {
    return Container(
      
    );
  }
}
```



## 二、项目结构

创建项目：flutter create myapp

* android   android平台相关的代码 
  ios       ios平台相关的代码 
  lib       flutter相关的代码，我们写的代码主要放在lib下面，类似于vue项目中的src文件夹
  test      测试   vue项目里面也有test文件夹  
  pubspec.yaml   配置文件   一般放一些第三方依赖   类似于vue项目中的paceage.json文件

* 在lib下面有一个main.dart，是入口文件，类似于vue中的main.js文件。

* runApp后面要跟上一个组件。vue中也有一个render(h) h(App)

* 在flutter中，一切都是组件，在vue中，一切也是组件，vue中的组件中.vue文件。在flutter中一个组件就是一个类，需要一个组件就是需要new这个类，通常可以把new省略不写。

* 在flutter中，结构和样式写在一起，当然它不像vue中分结构和样式，它是把样式也定义成了组件。

* 如果一个组件中包含了另一个组件，那么这个组件内部需要写一个child



## 三、组件

**一些常用的组件：**

- `Text` - 用于简单地在屏幕上显示文本的小部件。
- `Image` - 用于显示图像。
- `Icon` - 用于显示Flutter的内置Material和Cupertino图标。
- `Container` - 在Flutter中，相当于`div`。允许在其中进行添加填充，对齐，背景，力大小以及其他东西的加载。空的时候也会占用0px的空间，这很方便。
- TextInput - 处理用户反馈。
- `Row`, `Column`- 这些小部件显示水平或垂直方向的子项列表。
- `Stack` - 堆栈显示一个孩子的列表。这个功能很像CSS中的'position'属性。
- `Scaffold` - 为应用提供基本的布局结构。它可以轻松实现底部导航，appBars，后退按钮等。



###　1、Scaffold（脚手架）

`Scaffold` 是 Material组件库中提供的一个组件，它提供了默认的导航栏、标题和包含主屏幕widget树（后同“组件树”或“部件树”）的`body`属性。组件树可以很复杂。

* AppBar，通常显示在使用appBar属性的应用顶部。

* BottomNavigationBar，通常使用bottomNavigationBar 属性在应用程序底部显示，此功能向图中所示一样，无法做定制，只能以图片和文本形式存在。

* BottomAppBar，通常使用bottomNavigationBar属性显示在应用程序的底部，用来构建定制化的底部导航栏或者布局。

* FloatingActionButton，圆形按钮，通常使用floatingActionButton属性显示在应用程序的右下角。

* SnackBar，通常显示在应用程序底部附近的临时通知。
  

###　2、MaterialApp

 MaterialApp 是Material库中提供的Flutter APP框架，通过它可以设置应用的名称、主题、语言、首页及路由列表等。MaterialApp也是一个widget。一般写在最顶层



### 3、Center

`Center` 可以将其子组件树对齐到屏幕中心

此例中， `Center` 子组件是一个`Column` 组件，`Column`的作用是将其所有子组件沿屏幕垂直方向依次排列；



###　4、floatingActionButton

`floatingActionButton`是页面右下角的带“+”的悬浮按钮，它的`onPressed`属性接受一个回调函数，代表它被点击后的处理器，本例中直接将`_incrementCounter`方法作为其处理函数。



### 5、Text 

显示单一静态（不可编辑）的文本，类似Android中的**TextView**，IOS中的**label**。Flutter中Text提供了两个构造函数，首先介绍下上面代码中的**new Center**。

1. 文本对齐方式：TextAlign

- center：居中
- left：左对齐
- right：右对齐

1. 设置最多显示行数：maxLines
2. 文本溢出处理方式：overflow

- clip：直接切断
- ellipsis：省略号
- fade：渐变消失的效果，上下渐变

**new Center**：将子组件放置在自己的中心位置。

```dart
body:Center(
	child:Text(
		"123djasidhasuidsahyfgyugfdifgisadsadasd",
		textAlign:TextAlign.center,//对齐方式，还有 left、right
        maxLines:1,	//最多行数设置
        overflow:TextOverflow.ellipsis,//多于的内容如何处理，ellipsis是用省略号代替，fade是表示如果内容多余会从上到下颜色渐渐变淡（灰）
        style:TextStyle(
        	fontSize:25,//或许写为25.0
            fontWeight: FontWeight.bold, //字体粗细，也可以用w600等
            color:Color.fromRGBO(r,g,b,o),	//o代表透明度
            decoration:TextDecoration.underline,	//下划线
            decorationStyle:TextDecorationStyle.solid,	//下划线的样式设置
            letterSpacing:5, //字体之间的间距
        )，
	)，
)，
```





### 6、Icon

```
new Icon(Icons.add)			是一个小加号图标
```

在代码中我们会使用到很多的系统图标，这些图标的类型都是IconData，通过Icons即可获取。



### 7、Image

Flutter中获取图片的方式提供了以下几种：

```
加入图片的方式
Image.asset: 加载项目资源目录中的图片，加入图片后会增大打包的体积，使用相对路径。
Image.network: 加载网络资源图片。
Image.file: 加载本地文件中的图片，使用绝对路径。
Image.memory: 加载Uint8List资源图片。

fit属性
fill 全图显示，充满整个容器
contain 显示图片原比例
cover 保持图片不变形的前提下，充满整个容器
fitWidth 以宽度为基准充满容器
fitHeight 以高度为基准充满容器
scaleDown 效果和contain差不多，但是此属性不允许显示超过源图片大小，可小不可大

图片与color的混合模式（colorBlendMode）
color：要混合的颜色，单设置color的值无效
colorBlendMode：混合模式，设置混合的样式

图片的重复 （ImageRepeat）
repeat 铺满整个画布
repeatX 横向重复
repeatY 纵向重复
```

目前 Flutter的Image支持以下图像格式：JPEG，PNG，GIF，动画GIF，WebP，动画WebP，BMP和WBMP。

```
color: Color.fromRGBO(2, 0, 200, 1),	//设置图片颜色，为了设置蒙版
colorBlendMode: BlendMode.xxx,		//设置蒙版，与上面设置的颜色相关
height:50,	
width:100,	//设置图片的高度，宽度
repeat: ImageRepeat.repeatX ,  //设置当图片小于空间时图片的重叠方式
fit: BoxFit.fill,	//设置图片的填充方式
```



### 8、Row 和 Column

Row:横向布局，Column：纵向布局。这里和Android中的LinearLayout（线性布局）的 horizontal（横向）与 vertical（纵向）是一样的效果。

#### 水平布局Row

1. 非灵活排列
   根据Row子元素的大小进行布局，若子元素不足，它会留有空隙；若子元素超出，它会警告。
   例如：`RaisedButton`
2. 灵活排列
   使用Expanded解决有空隙的问题，在按钮外加入Expanded就可以了。

#### 垂直布局column

Column属性与Row基本相同

1. 主轴和副轴的辨识

- 主轴(main轴)：column组件主轴是垂直
                           row组件主轴是水平
- 副轴(cross轴)：column组件副轴是水平
                           row组件副轴是垂直

2. mainAxisAlignment(主轴对齐方式)

3. crossAxisAlignment(副轴对齐方式)

4. 水平方向相对于屏幕居中 在child外层嵌套Center组件

#### 处理富余空间

**MainAxisAlignment和CrossAxisAlignment简介**

MainAxisAlignment（主轴）和CrossAxisAlignment（交叉轴）常用于Row和Column控件中，主要是用来控制子控件排列的位置，并可以配合textDirection和verticalDirection属性来控制子控件排列的方向及改变MainAxisAlignment和CrossAxisAlignment的起始位置。
**MainAxisAlignment（主轴）就是与当前控件方向一致的轴，而CrossAxisAlignment（交叉轴）就是与当前控件方向垂直的轴**

```
mainAxisAlignment: MainAxisAlignment.xxx,

MainAxisAlignment {
  start,  //将子控件放在主轴的开始位置
  end,   //将子控件放在主轴的结束位置
  center,   //将子控件放在主轴的中间位置
  spaceBetween, //将主轴空白位置进行均分，排列子元素，手尾没有空隙
  spaceAround, //将主轴空白区域均分，使中间各个子控件间距相等，首尾子控件间距为中间子控件间距的一半
  spaceEvenly,  //将主轴空白区域均分，使各个子控件间距相等
}

CrossAxisAlignment {
  start,   //将子控件放在交叉轴的起始位置
  end,   //将子控件放在交叉轴的结束位置
  center,   //将子控件放在交叉轴的中间位置
  stretch,  //使子控件填满交叉轴
  baseline,  //将子控件放在交叉轴的上，并且与基线相匹配（不常用）
}
```

示例：

```
child: Row(
        mainAxisAlignment: MainAxisAlignment.end,
        crossAxisAlignment: CrossAxisAlignment.start,
        children: <Widget>[
          new Icon(Icons.search,color:Colors.red),
          new Icon(Icons.home,color:Colors.blue),
          new Icon(Icons.select_all,color:Colors.lightBlue),
          new Icon(Icons.send,color:Colors.red),
        ],
      ),
```



### 9、container

**Container：可以添加padding、margin、border、background color、通常用于装饰其他Widget**

1. child的对齐方式： Alignment

* 主要通过 left 、right、top、bottom、center、等进行组合，拼接位置，
* 例如：bottomCenter、botomLeft、center、centerRight、topRight、topCenter、

1. height:高度 width:宽度 color:颜色
2. margin: 内边距
   `EdgeInsets.all()`
   `EdgeInsets.fromLTRB()`
3. padding: 外边距
4. decoration: ( container 的修饰器，主要的功能是设置背景和边框)
   `decoration: BoxDecoration()`
   border: container的边框
   `border:Border.all(width:2.0,color:Colors.red)`

```dart
body:Center(
	child:Container(
    	child:new Text("hello world",style:TextStyle(fontSize:40.0)),
        alignment:Alignment.center, //例如bottomLeft，topRight,centerLeft等等
        width:500,
        height:400,
        //color:Color.lightBlue,	//背景颜色设置
        ///padding:const EdgeInsets.all(10),//all表示四边都是10
        padding:const EdgeInsets.fromLTRB(10,20,30,40),//这是分别设置四边
        margin:const EdgeInsets.all(10),
        decoration:new BoxDecoration(	//设置渐变色，但与color冲突，不能同时设置
        	gradient:const LineraGradient(
            	colors:[Colors.lightBlue,Colors.greenAccent,Colors.purple]
            )，
            border:Border.all(width:2.0,color:Colors.red),	//边框设置
            borderRadius:BorderRadius.all(Radius.circular(100)),	//圆角设置
            
        )
    )
)
```



### 10、ClipOval

实现圆形组件

```
ClipOval例子(默认全圆角):

new ClipOval(
            child: Container(
              width: 100,
              height: 100,
              color: Colors.red,
            ),
          ),
```



### 11、ListView

ListView内部的children使用了widget数组，因为是一个列表，所以它接受一个数组。

1. listTite列表瓦片

- leading 列表前的icon图标
- title 列表标题

1. 设置ListView的滚动方向(scrollDirection)

- Axis.horizontal:横向滚动或者叫水平方向滚动
- Axis.vertical:纵向滚动或者叫垂直方向滚动

### 1、ListView.builder

```dart
scrollDirection: Axis.vertical, //设置滑动方向 Axis.horizontal 水平  默认 Axis.vertical 垂直
padding: EdgeInsets.all(10.0),   //内间距      
reverse: false,  //是否倒序显示 默认正序 false  倒序true
primary: true,   //false，如果内容不足，则用户无法滚动 而如果[primary]为true，它们总是可以尝试滚动。
itemExtent: 50.0,  //确定每一个item的高度 会让item加载更加高效
shrinkWrap: true,  //内容适配
itemCount: list.length,  //item 数量
physics: new ClampingScrollPhysics(),  //滑动类型设置
cacheExtent: 30.0,   //cacheExtent  设置预加载的区域 
controller ,   //滑动监听

//要显示的数据
itemBuilder: (BuildContext context,int i){
	return Text('${_list[i]["data"]["description"]}');
},
```

分析几个比较难理解的属性

1. shrinkWrap特别推荐s

   child 高度会适配 item填充的内容的高度,我们非常的不希望child的高度固定，因为这样的话，如果里面的内容超出就会造成布局的溢出。

   shrinkWrap多用于嵌套listView中 内容大小不确定 比如 垂直布局中 先后放入文字 listView （需要Expend包裹否则无法显示无穷大高度 但是需要确定listview高度 shrinkWrap使用内容适配不会有这样的影响）

2. primary
   在构造中默认是false 它的意思就是为主的意思，primary为true时，我们的controller 滑动监听就不能使用了

3. physics

   这个属性几个滑动的选择
   AlwaysScrollableScrollPhysics() 总是可以滑动
   NeverScrollableScrollPhysics禁止滚动
   BouncingScrollPhysics 内容超过一屏 上拉有回弹效果
   ClampingScrollPhysics 包裹内容 不会有回弹

4. cacheExtent

   这个属性的意思就是预加载的区域
   设置预加载的区域 cacheExtent 强制设置为了 0.0，从而关闭了“预加载”

5. controller

   滑动监听，我们多用于上拉加载更多，通过监听滑动的距离来执行操作。



#### 2、ListTile

1. 利用了ListTile实现内部列表，任何容器组件其实都可以使用ListTile
2. Divider() 用来生成一条分割线

```dart
this.leading,              // item 前置图标
this.title,                // item 标题
this.subtitle,             // item 副标题
this.trailing,             // item 后置图标
this.isThreeLine = false,  // item 是否三行显示
this.dense,                // item 直观感受是整体大小
this.contentPadding,       // item 内容内边距
this.enabled = true,
this.onTap,                // item onTap 点击事件
this.onLongPress,          // item onLongPress 长按事件
this.selected = false,     // item 是否选中状态
```



### 12、GridView

1. `GridView`默认构造函数可以类比于`ListView`默认构造函数，适用于**有限个数子元素**的场景，因为`GridView`组件会一次性全部渲染`children`中的子元素组件；
2. `GridView.builder`构造函数可以类比于`ListView.builder`构造函数，适用于**长列表**的场景，因为`GridView`组件会根据子元素是否出现在屏幕内而动态创建销毁，减少内存消耗，更高效渲染；
3. `GridView.count`构造函数是`GrdiView`使用`SliverGridDelegateWithFixedCrossAxisCount`的简写（语法糖），效果完全一致；
4. `GridView.extent`构造函数式`GridView`使用`SliverGridDelegateWithMaxCrossAxisExtent`的简写（语法糖），效果完全一致。

```dart
return GridView.count(
      crossAxisCount: 2, // 一行多少个 
      crossAxisSpacing: 10,  // 列与列的间隔
      mainAxisSpacing: 10, // 行与行的间隔
      padding: EdgeInsets.all(10),
      childAspectRatio: 1, // 设置宽高比
      children: this._getData(),
    );
```



### 13、bottomNavigationBar 底部导航栏



**一个完整的底部导航页面**：点击底部导航，简单的页面跳转

```dart
import 'package:flutter/material.dart';
import 'package:flutter/widgets.dart';

import '../views/Home.dart';
import '../views/search.dart';
import '../views/Setting.dart';


class Tabss extends StatefulWidget {
  @override
  _TabssState createState() => _TabssState();
}

class _TabssState extends State<Tabss> {
  int _currentIndex = 0;  
  List _pageList = <Widget>[
    Home(),
    Search(),
    Setting()
  ];
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: this._pageList[this._currentIndex],
      bottomNavigationBar: BottomNavigationBar(
        type: BottomNavigationBarType.fixed,  //默认只能写三个，加上这句话可以配置多个
        fixedColor: Colors.red, //选中时的颜色设置
        iconSize: 20, //icon图标大小
        currentIndex: this._currentIndex,   //设置显示导航的索引
        onTap: (int index){     //onTap点击事件
          setState(() {
           this._currentIndex = index; 
          });
        },
        items: [
          BottomNavigationBarItem(
            icon: Icon(Icons.home),   //这里icon和title缺一不可。。。
            title: Text("首页")
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.category),
            title: Text("分类")
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.search),
            title: Text("搜索")
          )
        ],
      ),
    );
  }
}
```





### 14、按钮

一般常用的 Button 是 MaterialButton、IconButton、FloatingActionButton。

**注意：写按钮时都要加上onpress事件，否则可能会出现一些问题**

#### 1、MaterialButton

**MaterialButton** 是一个 Materia 风格的按钮。

```csharp
new MaterialButton(
    color: Colors.blue,
    textColor: Colors.white,
    child: new Text('点我'),
    onPressed: () {
        // ...
    },
)
```

#### 2、RaisedButton

**RaisedButton** 与 **MaterialButton** 类似。

```csharp
new RaisedButton(
    child: new Text('点我'),
    onPressed: () {},
)
```

#### 3、FlatButton

**FlatButton** 与 **MaterialButton** 类似，不同的是它是透明背景的。如果一个 Container 想要点击事件时，可以使用 FlatButton 包裹，而不是 MaterialButton。因为 MaterialButton 默认带背景，而 FlatButton 默认不带背景。



#### 4、IconButton

**IconButton** 顾名思义就是 Icon + Button 的复合体，当某个 Icon 需要点击事件时，使用 IconButton 最好不过。

```csharp
new IconButton(
    icon: new Icon(Icons.volume_up),
    tooltip: 'Increase volume by 10%',
    onPressed: () {
        // ...
    },
)
```

其外，还有已经定义好的 Icon Button：CloseButton、BackButton。他们都有导航返回的能力。

#### 5、FloatingActionButton

**FloatingActionButton** 是一个浮动在页面右下角的浮动按钮。

```cpp
new Scaffold(
    // ...
    floatingActionButton: new FloatingActionButton(
        onPressed: () {},
        child: new Icon(Icons.add_a_photo),
        elevation: 10,		//阴影设置
        highlightElevation: 2.0,
        backgroundColor: Colors.red,        // 红色
    ),
)
```

在 Scaffold 里使用的时候，它是一个浮动状态的按钮，在其他地方使用，就不会浮动了。

#### 6、ButtonBar

**ButtonBar** 是一个布局组件，可以让 Button 排列在一行。

```cpp
new ButtonBar(
    children: <Widget>[
        new CloseButton(),
        new BackButton(),
    ],
)
```

#### 7、样式

```dart
/*-------------------------------------按钮内部属性----------------------------------*/
child: Text("阴影按钮"),	//按钮文字
color: Colors.blue,		//按钮的背景颜色
textColor: Colors.white,	//按钮文字颜色
elevation: 20,		//阴影距离
//按钮的点击事件
onPressed: () {		
	 print("颜色按钮");
},
//设置按钮的圆角
shape: RoundedRectangleBorder(		
	borderRadius: BorderRadius.circular(20)
),
//将按钮变为圆形，并设置一个red颜色的边
shape: CircleBorder(
	side: BorderSide(color: Colors.red)
),

/*-----------------------------其他位置的按钮设置----------------------------------*/
//带有图标的按钮
RaisedButton.icon(
	icon: Icon(Icons.search),
	label: Text("图标按钮"),
	onPressed: () {
		print("图标按钮");
	},
),
SizedBox(height: 5),	//这个可用来设置按钮之间的距离 （写在按钮与按钮之间）
//设置按钮的宽高需要在按钮外设置
Container(
width: 200,
height: 50,
child: RaisedButton(
	child: Text("自定义按钮"),
	onPressed: () {},
	),
),
/*-----------------------------------------------------------------------------------*/
//靠左的按钮
leading: IconButton(	
	icon: Icon(Icons.menu),
	onPressed: (){},
),
//靠右的按钮
actions: <Widget>[
	IconButton(
	icon: Icon(Icons.settings),
	onPressed: (){},
)
```





#### 示例：

有点多余的东西，不要在意

```dart
class main11 extends StatefulWidget {
  @override
  _main11State createState() => _main11State();
}

class _main11State extends State<main11> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            RaisedButton(
              child: Text("变一下"),
              color: Colors.blue,
              textColor: Colors.white,
              textTheme: ButtonTextTheme.primary,
              onPressed: (){
                print("11111");
              },
            ),
          ],
        ),
      ),
    );
  }
}
```



### 15、表单



```dart
child: new TextField(
autocorrect: false, // 是否自动校正
autofocus: false, //自动获取焦点
enabled: true, // 是否启用
inputFormatters: [], //对输入的文字进行限制和校验
keyboardType: TextInputType.text, //获取焦点时,启用的键盘类型
maxLines: 2, // 输入框最大的显示行数
maxLength: 3, //允许输入的字符长度/
maxLengthEnforced: false, //是否允许输入的字符长度超过限定的字符长度
obscureText: true, // 是否隐藏输入的内容
//onChanged事件,在输入内容发生变化的时候触发
onChanged: (newValue) {
    // print(newValue); // 当输入内容变更时,如何处理
},
//onSubmitted事件,则是在输入结束,点击完成的时候触发。
onSubmitted: (value) {
    // print("whar"); // 当用户确定已经完成编辑时触发
},
style: new TextStyle(
    color: new Color(Colors.amberAccent.green)), // 设置字体样式
textAlign: TextAlign.center, //输入的内容在水平方向如何显示
decoration: new InputDecoration(
    labelText: "城市",	//在输入框上的左上角的文字,默认显示在输入框中，点击后移到左上角
    icon: new Icon(Icons.location_city),	//在输入框左侧的图标
    border: new OutlineInputBorder(), // 边框样式
    helperText: 'required',	//必须输入
    hintText: '请选择你要投保的城市', //默认文字，类似于placeholder
    prefixIcon: new Icon(Icons.android), //输入框内左侧的图标
    prefixText: 'Hello' //输入框左侧内部的文字，默认显示，但不会消失
),
```



在`TextFormField`中没有这onChange,onSubmitted两个事件,取而代之的是`validator`,`onSaved`,`onFieldSubmitted` 他们都接受三个函数,并且将其值作为参数传递到函数里面

- validator,如果开启`autovalidate: true`,那么将会自动检验输入的值,如果没有则会在表单提交的时候检验 该函数只允许返回验证失败的错误信息以及验证通过时返回null。
- onSaved, 当调用`FormState.save`方法的时候调用。
- onFieldSubmitted, 与`onSubmitted`一样,则是在输入结束,点击完成的时候触发。



**获取输入框的值：**

1. 给每个输入框声明一个变量

```
String _text1 = "";
String _text2 = "";		
```

1. 给每个输入框加上一个onChange事件,

```
onChanged: (value){
	setState(() {
		this._text2=value; 
});
```

3. 给登录按钮一个onPress点击事件

```
onPressed: (){
	print(this._text1);
	print(this._text2);
},
```



**输入框的初始值**

```dart 
//首先声明变量
var _text2 = new TextEditingController(); //初始化的时候给表单赋值
  @override			//这里的@override时多加的一个，原来的下面还要写
  void initState() {
    super.initState();
    _text2.text = '初始值';
  }

//在输入框设置onChanged
TextField(
	onChanged: (value) {
		setState(() {
		this._text2.text = value;
		});
	},
),
```



### 16、GestureDetector 手势控制

```dart

    GestureDetector({
    Key key,
    this.child,
    this.onTapDown,//可能导致点击的指针已联系到屏幕的特定位置
    this.onTapUp,//触发点的指针已停止在特定位置与屏幕联系
    this.onTap,//发生了点击。
    this.onTapCancel,//触发onTapDown的指针取消触发
    this.onDoubleTap,//双击
    this.onLongPress,//长按
    this.onLongPressUp,//长按结束
    this.onVerticalDragDown,//
    this.onVerticalDragStart,//指针已经接触到屏幕，而且可能开始垂直移动。
    this.onVerticalDragUpdate,//与屏幕接触并垂直移动的指针沿垂直方向移动
    this.onVerticalDragEnd,//以前与屏幕接触并垂直移动的指针不再与屏幕接触，并且当其停止接触屏幕时以特定速度移动。
    this.onVerticalDragCancel,//
    this.onHorizontalDragDown,//
    this.onHorizontalDragStart,//
    this.onHorizontalDragUpdate,//
    this.onHorizontalDragEnd,//
    this.onHorizontalDragCancel,//

//    onPan可以取代onVerticalDrag或者onHorizontalDrag，三者不能并存
    this.onPanDown,//指针已经接触屏幕并开始移动
    this.onPanStart,//与屏幕接触并移动的指针再次移动
    this.onPanUpdate,//先前与屏幕接触并移动的指针不再与屏幕接触，并且当它停止接触屏幕时以特定速度移动
    this.onPanEnd,//先前触发 onPanDown 的指针未完成
    this.onPanCancel,//

//    onScale可以取代onVerticalDrag或者onHorizontalDrag，三者不能并存，不能与onPan并存
    this.onScaleStart,//
    this.onScaleUpdate,//
    this.onScaleEnd,//
    this.behavior,
    this.excludeFromSemantics = false
    })

```



### 17、TabBar

```dart
//一些基本属性
  const TabBar({
    Key key,
    @required this.tabs,//子标签
    this.controller,//控制器
    this.isScrollable = false,//能否滑动,false：tab宽度则等比，true：tab宽度则包裹item
    this.indicatorColor,//指示器颜色
    this.indicatorWeight = 2.0,
    this.indicatorPadding = EdgeInsets.zero,
    this.indicator,
    this.indicatorSize,//TabBarIndicatorSize.label：indicator与文字同宽，TabBarIndicatorSize.tab：与tab同宽
    this.labelColor,//选中标签颜色
    this.labelStyle,//选中标签样式
    this.labelPadding,
    this.unselectedLabelColor,//未选中标签颜色
    this.unselectedLabelStyle,
    this.dragStartBehavior = DragStartBehavior.down,
    this.onTap,//点击事件
  })

```

配置步骤：

1、在appBar中配置基本的Tab选项

```dart
appBar: AppBar(
        title: TabBar(
          controller: _controller,	//这里设置控制器
          //appbar的属性设置都在这里
          indicatorColor: Colors.red,
          isScrollable: true,
          tabs: <Widget>[
            Tab(icon: Icon(Icons.directions_bike)),
            Tab(icon: Icon(Icons.directions_car)),
            Tab(icon: Icon(Icons.directions_subway)),
            Tab(icon: Icon(Icons.directions_bike)),
            Tab(icon: Icon(Icons.sort)),
            Tab(icon: Icon(Icons.star)),
            Tab(icon: Icon(Icons.shuffle)),
            Tab(icon: Icon(Icons.category)),
          ],
        )
      ),
```

2、一些配置

```dart
//with SingleTickerProviderStateMixin  后面加上的这个是实现切换动画的
class _MyTopBarState extends State<MyTopBar> with SingleTickerProviderStateMixin{
  // TabBar组件需要有一个控制器 
  TabController _controller;
    
//定义一个钩子,其中 @override 是钩子函数的
  @override
  void initState() {
    // 给导航控制器进行初始化
    // 需要在这个勾子函数中对TabBar进行初始化
    // _controller能够将页面和导航绑定到一起，最终实现切换的效果
    // length 导航/页面的个数  vsync动画效果异步匹配
    _controller = TabController(length: 3,vsync: this);
    super.initState();
  }

```

#### 完整页面代码

```dart
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';

class MyTopBar extends StatefulWidget {
  @override
  _MyTopBarState createState() => _MyTopBarState();
}

// extends表示继承 
// SingleTickerProviderStateMixin 对我们的组件进行配置，主要是配置动画切换效果
class _MyTopBarState extends State<MyTopBar> with SingleTickerProviderStateMixin{
  // TabBar组件需要有一个控制器 
  TabController _controller;
  var tabs = <Tab>[];	//这个是用来自动适应tabbar的数量的
  @override
  void initState() {
    // 给导航控制器进行初始化
    // 需要在这个勾子函数中对TabBar进行初始化
    // _controller能够将页面和导航绑定到一起，最终实现切换的效果
    // length 导航/页面的个数  vsync动画效果异步匹配
    _controller = TabController(length: tabs.length,vsync: this);
    super.initState();
  }
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: TabBar(
          controller: _controller,
          tabs: <Widget>[
            Tab(icon: Icon(Icons.directions_bike)),
            Tab(icon: Icon(Icons.directions_car)),
            Tab(icon: Icon(Icons.directions_subway)),
            Tab(icon: Icon(Icons.directions_bike)),
            Tab(icon: Icon(Icons.sort)),
            Tab(icon: Icon(Icons.star)),
            Tab(icon: Icon(Icons.shuffle)),
            Tab(icon: Icon(Icons.category)),
          ],
        )
      ),
      body: TabBarView(
        controller: _controller,
        children: <Widget>[
          SelfHomePage(page:1),
          SelfHomePage(page:2),
          SelfHomePage(page:3),
          SelfHomePage(page:4),
          SelfHomePage(page:5),
          SelfHomePage(page:6),
          SelfHomePage(page:7),
          SelfHomePage(page:8),
        ],
      ),
    );
  }
}
class SelfHomePage extends StatelessWidget {
  int page;
  // 类的一个重写，重写的目的就是类可以传递参数
  SelfHomePage({Key key, @required this.page}):super(key:key);
  @override
  Widget build(BuildContext context) {
    return Center(
      child: Text("page $page",style: TextStyle(fontSize: 40),),
    );
  }
}
```



## 四、路由

路由(Route)在移动开发中通常指页面（Page），这跟web开发中单页应用的Route概念意义是相同的，Route在Android中通常指一个Activity，在iOS中指一个ViewController。所谓路由管理，就是管理页面之间如何跳转，通常也可被称为导航管理。Flutter中的路由管理和原生开发类似，无论是Android还是iOS，导航管理都会维护一个路由栈，路由入栈(push)操作对应打开一个新页面，路由出栈(pop)操作对应页面关闭操作，而路由管理主要是指如何来管理路由栈。

### 1、简单示例：

1. 创建一个新路由，命名“NewRoute”

   ```dart
   class NewRoute extends StatelessWidget {
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         appBar: AppBar(
           title: Text("New route"),
         ),
         body: Center(
           child: Text("This is new route"),
         ),
       );
     }
   }
   ```

   新路由继承自`StatelessWidget`，界面很简单，在页面中间显示一句"This is new route"。

2. 在`_MyHomePageState.build`方法中的`Column`的子widget中添加一个按钮（`FlatButton`） :

   ```dart
   Column(
         mainAxisAlignment: MainAxisAlignment.center,
         children: <Widget>[
         ... //省略无关代码
         FlatButton(
            child: Text("open new route"),
            textColor: Colors.blue,
            onPressed: () {
             //导航到新路由   
             Navigator.push( context,
              MaterialPageRoute(builder: (context) {
                 return NewRoute();
              }));
             },
             //路由返回
             onTap: (){
               // pop后面可以跟上参数
             	Navigator.of(context).pop("旺财 北京市昌平区沙河镇北京科技经营管理学院");
          },
            ),
          ],
    )
   ```
   
   我们添加了一个打开新路由的按钮，并将按钮文字颜色设置为蓝色，点击该按钮后就会打开新的路由页面。



### 2、Navigator

> Navigator用来管理堆栈功能（即push和pop）

push和pop, push 是将元素添加到堆栈的顶部，pop是从同一堆栈中删除顶部元素。

在Flutter的情况下，当我们导航到另一个屏幕时，我们使用Navigator.push方法将新屏幕添加到堆栈的顶部。当然，这些pop方法会从堆栈中删除该屏幕。

context：代表上下文，也就是类似windows中的句柄，指的是当前的这个页面窗口。

```
Navigator.pop(context);
```

Navigator.pop(context)：pop在javascript中用于删除数组的最末一个元素，这就明白了，就是删除当前页面返回到Navigator中的前一个页面。

 





### 3、MaterialPageRoute

我们可以直接使用 `MaterialPageRoute`创建路由，它是一种模态路由，可以通过平台自适应的过渡效果来切换屏幕。默认情况下，当一个模态路由被另一个替换时，上一个路由将保留在内存中，如果想释放所有资源，可以将 `maintainState` 设置为 `false`。

示例：

```dart
onPressed: () {
  Navigator.push(
    context,
      //其中，new SecondScreen()是另一个界面
    new MaterialPageRoute(builder: (context) => new SecondScreen()),
  );
},
```

### 4.命名式路由(重点)

想vue中那样将路由分离出去,单独放到一个文件夹中 

- 引入组件 Route.dart
- 配置路由规则
- 统一配置处理

```js
import 'package:flutter/material.dart';
import '../pages/Tabs.dart';
import '../pages/Form.dart';
import '../pages/Search.dart';

// 配置路由规则 
final routes = {
  "/":(context)=>Tabs(),
  "/form":(context)=>FormPage(),
    //路由传值
  "/search":(context,{arguments})=>SearchPage(arguments:arguments),
};

// 如果你要把路由抽离出去，需要写下面这一堆的代码
var onGenerateRoute=(RouteSettings settings) {
      // 统一处理
      final String name = settings.name; 
      final Function pageContentBuilder = routes[name];
      if (pageContentBuilder != null) {
        if (settings.arguments != null) {
          final Route route = MaterialPageRoute(
              builder: (context) =>
                  pageContentBuilder(context, arguments: settings.arguments));
          return route;
        }else{
            final Route route = MaterialPageRoute(
              builder: (context) =>
                  pageContentBuilder(context));
            return route;
        }
      }
};
```

main.dart入口文件配置

- 导入路由文件

- 配置默认访问路径

  ```js
  import 'routes/Routes.dart'
  return MaterialApp(
  initialRoute:"/",//默认访问路径 一般是首页面
   onGenerateRoute: onGenerateRoute //必须写这行
  )
  ```


### 5.路由传值

```dart
//父组件
onTap:(){
    Navigator.pushName(context,"/newscontent",arguments:{"aid":this._list[index]["aid"]})
}
//子组件接收
class NewsContent extends StatefulWidget {
  //接收传递过来的数据
   Map arguments;//先定义类型
  NewContent({Key key,this.arguments}):super(key :key);
  @override
  _NewContentState createState() => _NewContentState(this.arguments);
}
```



























## 五、数据操作

### 1、子传父

```dart
/*----------------------------------获取值的兄弟组件------------------------------------*/
var _data = "";
.....//中间的语句省略
RaisedButton(
	child: Text("跳转到search"),
	onPressed: () async{
		//导航到新路由
		var data = await Navigator.push(context, MaterialPageRoute(builder: (context) {
		return Search();
		}));
		setState(() {
			 _data =  data;
		});
	},
    //这个也可以写为
    /*onPressed: () {
		//导航到新路由
		var data = await Navigator.push(context, MaterialPageRoute(builder: (context) {
		return Search();
		})).then((res){
			setState(() {
				 _data =  data;
			});
		});
	},	*/
	
),
Text("${ _data =='' ? '无内容':_data}"),

/*---------------------------------发送值的兄弟组件------------------------------------*/

body: ListView(
	children: <Widget>[
		RaisedButton(
			child: Text("返回1"),
			onPressed: () {
				Navigator.of(context).pop("搜索内容11111");
			},
		),
		RaisedButton(
			child: Text("返回2"),
			onPressed: () {
				Navigator.of(context).pop("搜索内容22222");
			},
		),
	],
)

```



### 2、父传子

```dart
/*----------------------------------父组件--------------------------------------------*/
RaisedButton(
	child: Text("表单"),
	onPressed: () {
	Navigator.of(context).push(MaterialPageRoute(
		builder: (context) => FormPage(title: "表单数据xxx")));
	},
),

/*-----------------------------------子组件-------------------------------------------*/

class FormPage extends StatelessWidget {
  String title;
  FormPage({this.title = ""});		//需要在这里进行定义，并可以设置初始值，在没有传值时显示
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(this.title)),	//在这里接收
      body: Text("body"),
      floatingActionButton: FloatingActionButton(		//只是一个返回按钮
        child: Text("返回"),
        onPressed: () {
          Navigator.of(context).pop();
        },
      ),
    );
  }
}
```

### 3、网络数据请求

首先安装依赖,在使用的地方导入

```js
dependencies:
  dio: ^2.0.0
  http: ^0.12.0+2
//导入
import 'package:http/http.dart' as http;
import 'package:dio/dio.dart';
import 'dart:convert';
```

#### 1、dio

```js
List _list=[];
@override
  //钩子函数
  void initState() {
    // TODO: implement initState
    super.initState();
    //this._getData();//加载时直接触发钩子函数
    this._dioGetData();
  } 
_dioGetData() async{
     var apiUrl="https://movie.douban.com/j/search_subjects?type=movie&tag=热门&page_limit=50&page_start=0";
     Response response=await Dio().get(apiUrl);
     setState(() {
      this._list=response.data["subjects"] ;
     });
     //print(response);
  }
```



#### 2 、http

## 六、flutter生命周期

![RUNOOB 图标](https://gitee.com/winter_winter_and_winter/images/raw/master/flutter%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)

大致可以看成三个阶段

- 初始化（插入渲染树）
- 状态改变（在渲染树中存在）
- 销毁（从渲染树种移除）

### 1、构造函数

这个函数不属于生命周期，因为这个时候State的widget属性为空，如果要在构造函数中访问widget的属性是行不通的。但是构造函数必然是要第一个调用的。

### 2、initState

```
/// Called when this object is inserted into the tree.
```

当插入渲染树的时候调用，这个函数在生命周期中只调用一次。这里可以做一些初始化工作，比如初始化State的变量。

### 3、didChangeDependencies

```
/// Called when a dependency of this [State] object changes.
```

这个函数会紧跟在initState之后调用，并且可以调用BuildContext.inheritFromWidgetOfExactType，那么BuildContext.inheritFromWidgetOfExactType的使用场景是什么呢？最经典的应用场景是

```
new DefaultTabController(length: 3, child: new TabBar(
      tabs: [ "主页","订单","我的" ]
      .map( (data)=>new Text(data) ).toList(),
```

### 4、didUpdateWidget

> /// Called whenever the widget configuration changes.

当组件的状态改变的时候就会调用didUpdateWidget,比如调用了setState.

实际上这里flutter框架会创建一个新的Widget,绑定本State，并在这个函数中传递老的Widget。

这个函数一般用于比较新、老Widget，看看哪些属性改变了，并对State做一些调整。

需要注意的是，涉及到controller的变更，需要在这个函数中移除老的controller的监听，并创建新controller的监听。

### 5、deactivate

> /// Called when this object is removed from the tree.

在dispose之前，会调用这个函数。

### 6、dispose

> /// Called when this object is removed from the tree permanently.

一旦到这个阶段，组件就要被销毁了，这个函数一般会移除监听，清理环境。



### 小结

| 阶段                  | 调用次数 | 是否支持setState               |
| --------------------- | -------- | ------------------------------ |
| 构造函数              | 1        | 否                             |
| initState             | 1        | 无效(使用setState和不使用一样) |
| didChangeDependencies | >=1      | 无效                           |
| didUpdateWidget       | >=1      | 无效                           |
| deactivate            | >=1      | 否                             |
| dispose               | 1        | 否                             |

## 七、

