# vue.js

Vue (读音 /vjuː/，类似于 **view**) 是一套用于构建用户界面的**渐进式框架**。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与现代化的工具链以及各种支持类库结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

v-bind	将这个元素节点的 `title` 特性和 Vue 实例的 `message` 属性保持一致





## 一、vue 基础



### 1. vue.js 的基本架构

```vue
<div id="app">
    
</div>
<!-- 引入vue.js文件 -->
<script src="../js/vue.js"></script>
<script>
    var app = new Vue({
        el:"#app",
        data:{
            
        }
    })
</script>
```
### 2. vue的一些指令

指令（Directives）是**写在html标签上**的带有 **v-** 前缀的特殊属性。指令的职责就是当其表达式的值改变时相应地将某些行为应用到 DOM 上。

常用指令包括如下:

-  数据绑定指令:把数据作为元素的内容显示出来。

> ​	**v-text**	
>
> ​	**v-html**	
>
> ​	**v-model**	

-  属性指令：把数据设置成为元素的属性.例class,src等。

> ​	**v-bind**	

- 条件指令:

> ​	**v-if	 v-elseif	 v-else**	
>
> ​	**v-show**	

-  循环指令

> ​	**v-for**	



#### 1.  v-text 

`v-text` 等价于插值语法{{ }}。只能解析文本，将解析之后的文本作为标签的内容。

```vue
<p v-text="ad"></p>   ==  <p>{{ad}}</p>			

data:{ad:"abcdefg"}
```



#### 2. v-html

  与`v-text`类似。但它可以解析html标签。

```vue
<p v-html="ac">	</p>    

data：{ac:"<h1>这是一个标题</h1>"}
```



#### 3. v-model

实现双向数据绑定

对标签有要求。它不能加在span标签上：因为span不能获取用户的输入，用户不能直接去修改span的内容。

它更加适应加在表单元素上。例如 input。用户可以去更改input的内容。

```vue
p>{{ad}}</p>
<input v-model="ad" type="text"/>			//data:{ad:"初始值，也可以为空"}
```



#### 4. v-bind

<标签 **v-bind:**属性名=”data配置项设置的属性名 ”>

​	有两个属性因为用的非常的多，所以它单独地设置了特殊用法：

​						style				Class

```vue
<!-- 完整语法 -->
<a v-bind:href="url">...</a>

<!-- 缩写 -->
<a :href="url">...</a>
```



#### 5. v-if

` v-if ` 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 true 的时候被渲染。

也可以用 `v-else` 添加一个“else 块”：

```vue
<p v-if="false">1111111</p>
<p v-else>22222222</p>

<!--v-else 必须紧跟在带v-if 或者 v-else-if 的元素的后面，否则它将不会被识别。可以使用 v-else-if 指令来形成嵌套条件循环-->

<p v-if="true">1111111</p>	

<!--v-if指令后的true或false也可用一个表达式来代替,但要用引号包起来-->
```

​			**因为 `v-if` 是一个指令，所以必须将它添加到一个元素上。但是如果想切换多个元素呢？此时可以把一个 `<template>` 元素当做不可见的包裹元素，并在上面使用 `v-if`。最终的渲染结果将不包含 `<template>` 元素。**





#### 6. v-show

用于根据条件展示元素的选项是 `v-show` 指令。用法与`v-for`大致一样

**不同的是带有 `v-show` 的元素始终会被渲染并保留在 DOM 中。`v-show` 只是简单地切换元素的 CSS 属性 `display`。**

```vue
<p v-show="true">1111111</p>
```





#### 7. v-if 	VS 	v-show

`v-if` 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。

`v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

相比之下，`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

v-if支持template标签，v-show不支持



#### 8. v-for

**<标签 v-for=“(value,key,index) in obj”></标签>**

```vue
<p v-for="i in 10" :key="i">{{value}}</p>		//从一到十
//v-for也支持template
```

1. obj就是定义在data配置项中的数据。
2. 它会产生多个标签
3. Value,index,key只是形参名，可以更改，并根据需要，可按顺序省略
4. vue2.5以上，使用v-for需要加上key，key要唯一
5. 可供循环的数据有：
   - 数组		对数组循环时，第三个参数没有用。
   - 对象
   - 整数         对整数循环时，从一开始，索引从0开始





## 二、`vue`的配置项



### 1. 方法 `methods`

`New Vue()`会设置一些配置项，如data表示数据。这些配置项有一个就是methods,它是可选，可以写也可以不写。

它的作用就相当于你可以写一些的逻辑代码，像函数一样去调用。

​	调用方法一 ：在 `vue `实例 的外面，通过 `app.f1()`, `app.f2()` 去访问。

​	调用方法二 ：在 `vue` 实例内部，直接调用。	一般在{{}} 或者是v-bind中使用

​		**在一个方法中调用另一个方法，需要加上this**

```vue
<div id="app">
	{{f1()}}			//在 vue 实例内部，直接调用。
</div>



<script>
var app = new Vue({
	el: "#app",
	data: {
        
	},				//这里要用逗号隔开
	methods:{
        f1(){
            
        },			//各个函数之间也要用逗号隔开
        f2(){
            
        }
	}
})
</script>
```



### 2. 计算属性 `computed`



计算属性computed也是 vue 实例的配置项。声明式的描述一个值依赖了其它的值.

用法：一般是对初始数据（data中数据项）进行加工，处理，返回新的数据。

调用：计算属性本质上也是一个函数，但在调用时与普通数据项一样，不需要加()	。

```vue
<script>
    Var vm = new Vue({
        el: ”#app”,
        data: {},
        methods: {},
        computed: {
            计算属性名: function () {
                return 值; //一定会有返回值
            }
        }
    })
</script>
```

​	**计算属性与方法之间的区别**：

1. 计算属性有缓存，函数没有缓存！

2. 函数比较敏感

3. 使用的区别 计算属性虽然本质也是方法，但它不加() ,方法要加()

   ##### 计算属性与watch之间的区别：

   - watch中可以写以异步



### 3. 过滤器 `filters`

过滤器是用来格式化数据。与 `el` , `data `, `methods`, `coputed `一样，也是一个 `vue `实例的配置项。

格式：	**{{数据| 过滤器}}**

多个过滤器一起使用：

格式：**{{数据项 | 过滤器1 | 过滤器2 }}**  

过滤器将数据在被指令处理并显示到视图之前进行转换，而**不必修改作用域中原有的数据**，这样能够允许同一份数据在应用的不同部分以不同形式得以展示。

```vue
<div id="app">
	<p>{{f|fixss(1)}}</p>
</div>
<!-- 引入vue.js文件 -->
<script src="../js/vue.js"></script>		
<script>
	var app = new Vue({
		el: "#app",
		data: {
			f: 3.14154,
		},
		methods: {},
		computed: {},
		filters: {
			fixss(d，n) {				
				return d.toFixed(n)
			}
		}
	})
</script>
```

在写过滤函数时，注意参数在传递的过程中的对应关系：

1. 第一个参数(必填)	是你一定要过滤的数据项。
2. 第二个参数(可选)	后面的参数就是你写在()中的实参

在调用时，如果有两个或两个以上的参数，第一个参数不需要填写，直填写第二个及以后的参数

 

过滤器的特殊之处在：

- 它的定义时，**至少要有一个参数**，而这个参数在调用时并不需要给实参：第一个参数默认的实参值就是你要过滤的数据项。
- 它**一定要有返回值 **, 这一点与计算属性是一致的。





## 三、事件



### 1.简介

1. **vue通过v-on指令处理事件**

可以用 `v-on` 指令监听 DOM 事件，并在触发时运行一些 JavaScript 代码。

**格式**：	<**事件源**标签 v-on:**事件类型**=“**事件处理函数**”></事件源标签>

```vue
<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>
<!-- 缩写 -->
<a @click="doSomething">...</a>
```

范例：

```vue
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>

<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
</script>
```

然而许多事件处理逻辑会更为复杂，所以直接把 JavaScript 代码写在 `v-on` 指令中是不可行的。因此 `v-on` 还可以接收一个需要调用的方法名称。



### 2. 书写位置

1. 如果代码比较简单：直接写在行内

2. 如果代码比较复杂：在methods中新加一个方法，然后在v-on中调用



### 3. 事件类型

- 鼠标事件，click，dblclick，mouseover，mouseout，mouseenter，mouseleave，mousedown，mousemove，mouseup
- 键盘事件，keydown，keypress，keyup，
- ui事件，scroll，resize，load，unload
- 焦点事件，focus，blur
- 表单事件，change，submit



在事件处理程序中调用 `event.preventDefault()` 或 `event.stopPropagation()` 是非常常见的需求。尽管我们可以在方法中轻松实现这点，但更好的方式是：方法只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。

为了解决这个问题，Vue.js 为 `v-on` 提供了**事件修饰符**。

- `.stop`

- `.prevent`

- `.capture`

- `.self`

- `.once`

- `.passive`

  ```vue
  <!-- 阻止单击事件继续传播 -->
  <a v-on:click.stop="doThis"></a>
  
  <!-- 提交事件不再重载页面 -->
  <form v-on:submit.prevent="onSubmit"></form>
  
  <!-- 修饰符可以串联 -->
  <a v-on:click.stop.prevent="doThat"></a>
  
  <!-- 只有修饰符 -->
  <form v-on:submit.prevent></form>
  
  <!-- 添加事件监听器时使用事件捕获模式 -->
  <!-- 即元素自身触发的事件先在此处理，然后才交由内部元素进行处理 -->
  <div v-on:click.capture="doThis">...</div>
  
  <!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
  <!-- 即事件不是从内部元素触发的 -->
  <div v-on:click.self="doThat">...</div>
  ```

  使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 `v-on:click.prevent.self` 会阻止**所有的点击**，而 `v-on:click.self.prevent` 只会阻止对元素自身的点击。

**按键修饰符**？

在监听键盘事件时，我们经常需要检查详细的按键。Vue 允许为 v-on 在监听键盘事件时添加按键修饰符：

```vue
<!-- 只有在 `key` 是 `Enter` 时调用 `vm.submit()` -->
<input v-on:keyup.enter="submit">

<!--你可以直接将 KeyboardEvent.key 暴露的任意有效按键名转换为 kebab-case 来作为修饰符。-->
<input v-on:keyup.page-down="onPageDown">
```

在上述示例中，处理函数只会在 $event.key 等于 PageDown 时被调用。

有如下按键修饰符：

.enter			.tab			.delete			.esc			.space			.up			.down			.left

.right			.ctrl  			.alt  				.shift  		.meta  





## 四、表单输入绑定



你可以用 `v-model` 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。尽管有些神奇，但 `v-model` 本质上不过是语法糖。它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理。

**双向数据绑定：只有表单元素上，才会是双向的。**

**`v-model` 会忽略所有表单元素的 `value`、`checked`、`selected` 特性的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 `data` 选项中声明初始值。**



`v-model` 在内部为不同的输入元素使用不同的属性并抛出不同的事件：

- text 和 textarea 元素使用 `value` 属性和 `input` 事件；
- checkbox 和 radio 使用 `checked` 属性和 `change` 事件；
- select 字段将 `value` 作为 prop 并将 `change` 作为事件。

### 1. 单选框

- 多个input:radio 的name要相同。这样才是单选
- 多个input:radio 的v-model要相同.
- 你设置的初值，必须要与input:radio中的value值相对应。

```vue
<!-- 性别选择并显示-->
<div id="app">
        <input type="radio" name="sex" v-model="add" value="男">男
        <input type="radio" name="sex" v-model="add" value="女">女
        <h1>{{add}}</h1>
    </div>
<!-- 引入vue.js文件 -->
<script src="../js/vue.js"></script>
    <script>
        var app = new Vue({
            el: "#app",
            data: {
                add:""
            },
            methods: {},
            computed: {},
            filters: {}
    })
</script>
```



### 2.复选框

一般有两种用法：

（1）只有一个复选框。用来表示 “同意”与否。表示单选，通常使用布尔值来表示。

```vue
<input type="checkbox" v-model="agree">
```

（2）多个复选框。表示在多个选择项中选择某一个，或者是几个。表示多选，通常使用数组来表示。例如，统计兴趣爱好，，可使用 **v-model** 使数据绑定



```vue
<!--是否同意按钮-->
<div id="app">
        <input type="checkbox" v-model="add" >同意
        <h1>{{f1()}}</h1>
</div>
<script src="../js/vue.js"></script>
<script>
	var app = new Vue({
		el: "#app",
		data: {
			add:"true"
		},
		methods: {
			f1(){return this.add?"同意":"不同意"}
		},
		computed: {},
		filters: {}
	})
</script>
```



```vue
<!--多选按钮-->

<div id="app">
	<template v-for="ass in allfav">
		<input v-model="info.favoriate" type="checkbox" :value="ass.value">{{ass.name}}
	</template>
	<hr/>
	<p>{{info}}</p>
</div>

<script src="../js/vue.js"></script>
<script>
	var app = new Vue({
	el: "#app",
	data: {
		allfav: [
			{name: "读书",value: "book"},
			{name: "唱歌",value: "song"},
			{name: "游戏",value: "game"},
			{name: "睡觉",value: "sleep"}
		],
		info: {favoriate: ["book"]}
	},
	methods: {},
	computed: {},
	filters: {}
	})
</script>
```



### 3. 下拉列表

使用`select`与`option`指令制作下拉列表

```vue
   <!--一个简单的下拉列表-->

	<div id="app">
        <select v-model="info.study" name="" id="">
            <option value="-请选择-">-请选择-</option>
            <option v-for="i in eduOption" v-model:value="i">{{i}}</option>
        </select>
        <hr/>
        <p>{{info}}</p>
    </div>
	<!-- 引入vue.js文件 -->
    <script src="../js/vue.js"></script>		

    <script>
        var app = new Vue({
            el: "#app",
            data: {
                info: {study:"-请选择-"},
                eduOption:["重点","一本","二本","三本"],
            },
            methods: {},
            computed: {},
            filters: {}
        })
    </script>
```



## 五、修饰符

#### `.lazy`

在默认情况下，`v-model` 在每次 `input` 事件触发后将输入框的值与数据进行同步 (除了[上述](https://cn.vuejs.org/v2/guide/forms.html#vmodel-ime-tip)输入法组合文字时)。你可以添加 `lazy` 修饰符，从而转变为使用 `change` 事件进行同步：

```vue
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg" >
```

#### `.number`

如果想自动将用户的输入值转为数值类型，可以给 `v-model` 添加 `number` 修饰符：

```vue
<input v-model.number="age" type="number">
```

这通常很有用，因为即使在 `type="number"` 时，HTML 输入元素的值也总会返回字符串。如果这个值无法被 `parseFloat()` 解析，则会返回原始的值。

#### `.trim`

如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符：

```vue
<input v-model.trim="msg">
```

## 六、 组件



在vue当中，一切都是组件。

组件就是我们自己扩展的 “HTML 元素”，封装可重用的代码。

在较高层面上，组件是自定义元素， Vue.js 的编译器为它添加特殊功能。

组件（Component）是 Vue.js 最强大的功能之一。

### 1.根组件

在` let vm = new Vue({}) `我们得到的vm是一个Vue的一个对象（或者叫实例），它也可以理解为一个组件，只不过它是一切其它的组件的根组件。

以示区别，我们后面都说是定义“子组件”

### 2. 子组件

有两种方法，使用组件：

- 全局组件
- 局部组件

#### 1. 全局组件

通过Vue的静态方法component()定义一个全局组件

**template:就是组件要显示出来的html标签的内容，它就会去替换你定义的html标签**

**全局组件：所有的vue实例都可以使用**

```vue
<div id="app">
    <!--这里使用注册名进行引用子组件-->
	<my-header></my-header>     
	<!-- 注意，如果组件名含有大写字母，需要在大写字母前加-并换为小写字母 -->
</div>

<!-- 引入vue.js文件 -->
<script src="../js/vue.js"></script>
<script>
   <!--全局组件-->
Vue.component("myHeader",{      //myHeader为组件名，可随意更改
    template:"<div>这是全局组件<div>"   //固定格式，引号内部可改
})
    
let app=new Vue({
    el:"#app",
    data:{},
    methods:{},
    conputed:{},
    filters:{},
})
</script>
```



#### 2. 局部组件

局部组件只能在注册的那个实例当中使用，不能在其它的实例中用。

##### 1. 简单局部

```vue
    <div id="app">
        <!--这里使用注册名进行引用子组件-->
        <myhead></myhead>		
        <son></son>
    </div>

<!-- 引入vue.js文件 -->
<script src="../js/vue.js"></script>
<script>
    // 可以在这里定义组件
    let myhead={
        template:"<div>这是一个根组件</div>"
    }
    let app = new Vue({
        el: "#app",
        data: {},
        components: {
            myhead:myhead,  //这里是注册组件,如果注册名与定义名一致的话可以直接写为 myhead, 这种简写格式
            "son": {        //这里是定义与注册一起完成，省略了定义名
                template:"<div>这是另一个根组件</div>"
            }
        }
    })
  </script>  
```

##### 2. 稍复杂局部组件

```vue
<div id="app">
        <son></son>
    </div>
    <!-- 当子组件过于复杂时，可将子组件写在外部，然后通过id名引用  -->
    <template id="son">
        <div>
            <button></button>
        </div>
    </template>
</body>
<script src="../js/vue.js"></script>
<script>
    let son = {
        template: "#son",
        data() {            //子组件这里data是函数格式，且必须返回一个对象
            return {
                //子组件中的数据项写这里，固定写法
            }
        }
    }
    let app = new Vue({
        el: "#app",
        data: {},
        methods: {},
        components: {
            son     //注册子组件，这里因为注册名与定义名一致，所以简写了
        }
    })
</script>
```



3. 一些细节

##### 1. 定义名

一般采用大驼峰命令法：如果有多个单词，则每个单词的首字母都要大小写。

例如：		MyHeader	EleUser

##### 2. 注册名

这里名字没有要求。一般与组件名保持一致：利用es6中的对象的属性名的简写规则，进行简写：

例如：		MyHeader : MyHeader,		可简写为	MyHead，

##### 3. 使用名

**在使用组件时，它的名字只能是全小写！！**

如果说你的组件的前两个名字：

1. 定义名字
2. 注册名字

都采用大驼峰命名法，则，使用组件你必须用kebab命名法：即大写字母改小写，在前面加-



**总结**：建议写法

| 定义名       | 注册名                        | 使用名      |
| ------------ | ----------------------------- | ----------- |
| 大驼峰命名法 | 与定义名保持一致。用es6的简写 | kebab命名法 |



##### 4. 子组件格式

```vue
<script>
let son = {				//son为组件名，自命名
	template:"",	
	data:function(){		//这里可简写为	data(){return{	//数据	}}
		return {
		//你的数据项
		}
	}
}
</script>
```

## 七、 组件之间的通信

#### 1.父传子

简单来讲就是：父组件中有数据，传递给子组件。步骤如下

- 首先确定父组件中有数据
- 在父组件的模板中通过属性绑定把数据绑到子组件上
- 在子组件中定义props属性，用来接收父传递过来的基础
- 在子组件的模板就可以使用接收过来的数据了

```vue
<body>
    <div id="app">
        我是父组件中的数据{{num}}
        <son :sonvalue="num"></son>
        //属性绑定
    </div>
    <template id="son">
        <div>
            <h2>我是子组件,我想用父组件中的数据{{sonvalue}}</h2>
            //这里就可以拿到父子组件的数据了
        </div>
    </template>
</body>
 <script>
    let Son ={
        template:"#son",
        data(){
             return{
             }
        },
        //props属性用来接收数据
        props:{
            sonvalue:Number,
        }
    }
    let app = new Vue({
         el:"#app",
         data:{
             num:100,
         },
         components:{
             Son
         }
    })
</script>
```

总结: prop 只可以从上一级组件传递到下一级组件（父子组件），即所谓的单向数据流。而且 prop 只读，不可被修改，所有修改都会失效并警告。

#### 2.子传父

```vue
<body>
    <div id="app">
        <h1>父组件</h1>
        <!-- 给子组件添加一个事件监听 -->
        <!-- submitmsg是一个事件 -->
        <!-- 子组件会发射一个事件，事件发生时会触发addmsg函数 -->
        <!-- addmsg是父组件的函数 -->
        <son @submitmsg="addmsg"></son>
        <h4>从子组件中传过来的数据{{a}}</h4>
    </div>
    <template id="son">
        <div>
            <h3>子组件</h3>
            <button @click="fashe">发射</button>
        </div>
    </template>
</body>
<script>
    let Son = {
        template: "#son",
        data() {
            return {
            }
        },
        methods: {
            fashe() {
                this.$emit("submitmsg",66666)
            }
        }
    }
    let vm = new Vue({
        el: "#app",
        data: {
            a:0},
        components: {
            Son
        },
        methods: {
            addmsg(info) {
                // console.log(info)
                this.a = info            }
        }
    })
</script>()
```



