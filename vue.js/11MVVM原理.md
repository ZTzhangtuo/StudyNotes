### **概念**

重点面试题:你对Vue中的mvvm有什么理解?

答:首先MVVM是一种软件架构设计模式,由MVC，MVP等设计模式进化而来

- Model层(M):模型层,负责处理业务逻辑以及和服务器进行交互
- View层(V):视图层,负责将数据转化为UI展示出来
- ViewModel层(Vm):视图模型层,用来连接Model和View,是Model和View之间的通信桥梁
- ![img](https://user-gold-cdn.xitu.io/2019/8/19/16ca75871ec53fba?imageslim)

小结:在这种设计模式下,将视图层和模型层分离开,并且通过双向数据绑定将这两者连接起来,是view和model的同步工作是完全自动的,因此开发者只需要专注于业务逻辑,不需要再手动操作DOM,显著提高开发效率

### 实现原理

主要通过四个步骤来实现双向数据绑定

- Compile 编译模板
- Observer 数据劫持
- Watcher  数据监听(观察者模式)
- Dep 发布订阅

![img](https://user-gold-cdn.xitu.io/2018/7/25/164cde75b446f4a4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

具体实现

一.实现一个解析器Compile,分别对DOM结构中的元素节点,和文本节点,进行编译.获得带有vue指令的节点,在对应的节点上绑定更新函数,

二.实现一个监听器Observer,根据数据劫持(Object.defineProperty())的原理,遍历实例中所有数据对象,并且加上getter,setter方法,那么就能监听到了数据变化。

三.实现一个Watcher,是Compile和Observer之间通信的桥梁,作为dep模式的订阅者,订阅Observer中属性值的变化,一旦数据变化,执行它在Compile里的回调,进而触发节点的更新函数

四.实现一个dep订阅器,采用订阅,发布模式,用来收集所有的Watcher订阅者,数据发生改变时,通知wachter,执行回调(update方法)

代码

```js
//观察者模式中包含发布-订阅模式 被观察者中包含观察者
//发布-订阅模式 发布和订阅之间没有必要的联系

//储存观察者的类Dep
class Dep{
    constructor(){
        this.subs = []; // subs中存放所有的watcher
    }
    // 添加watcher  订阅
    addSub(watcher){
        this.subs.push(watcher)
    }
    // 通知 发布  通知subs容器中所有的观察者
    notify(){
        this.subs.forEach(watcher=>watcher.update())
    }
}

// 观察者
class Watcher{
    constructor(vm,expr,cb){
        this.vm = vm;
        this.expr = expr;
        this.cb = cb;  // cb表示当状态改变了，要干的事
        // 刚开始需要保存一个老的状态
        this.oldValue = this.get();
    }
    // 获取状态的方法
    get(){
        Dep.target = this;
        let value = CompileUtil.getVal(this.vm,this.expr);
        Dep.target = null;
        return value;
    }
    // 当状态发生改变后，会调用观察者的update方法
    update(){
        let newVal = CompileUtil.getVal(this.vm,this.expr);
        if(newVal !== this.oldValue){
            this.cb(newVal);
        }
    }
}

//数据劫持类 实现数据的响应式
class Observer{
    constructor(data){
       //console.log(data)
        this.observer(data)
    }
    //把上面的数据变成响应式数据 把一个对象数据做成响应式
    observer(data){
        if(data && typeof data=='object'){
           // console.log(data)
            //for in 便利一个js对象
            for(let key in data){
                //console.log(key)//school
               // console.log(data[key]) //{name: "北京大学", age: 100}
                this.defindReactive(data,key,data[key])
            }

        }
    }
    defindReactive(obj,key,value){
        this.observer(value);
        let dep =new Dep();//不同的watcher放到不同的dep中
        Object.defineProperty(obj,key,{
            //当你获取school时,会调用get
            get(){
                Dep.target && dep.subs.push(Dep.target)
                //console.log("get...")
                return value
            },
            //设置school时,会调用set
            set:(newVal)=>{
                //当赋的值和老值一样,就不会重新赋值
                if(newVal!=value){
                   // console.log("set...")
                    this.observer(newVal)
                    value=newVal
                    //到此(数据劫持)监听数据变化功能已将完成
                    dep.notify()
                }
            }
        })
    }
}

//写一个对象,里面包含不同指令对应的不同的处理方法
CompileUtil={
    getVal(vm,expr){
       //console.log(expr)// school.name
       //console.log(vm)
       //归并取值
       return expr.split(".").reduce((data,current)=>{
        //console.log( data[current])
        return data[current]
    },vm.$data);
    },
    setVal(vm,expr,value){
        //console.log(expr) //school.name
        //console.log(value) //北京大学
        expr.split(".").reduce((data,current,index,arr)=>{
            //console.log(data)
            if(index==arr.length-1){
                return data[current]=value
            }
            return data[current]
        },vm.$data)

    },
    model(node,expr,vm){
        let fn=this.updater["modelUpdater"]
        //console.log("处理v-model指令的方法")
        new Watcher(vm,expr,(newVal)=>{
            fn(node,newVal)
        })
        node.addEventListener("input",(e)=>{
            //console.log(e.target.value)
            let value=e.target.value
            this.setVal(vm,expr,value);
        })
         let value=this.getVal(vm,expr)
         fn(node,value);
    },
    getContentValue(vm,expr){
        return expr.replace(/\{\{(.+?)\}\}/g,(...args)=>{
            return this.getVal(vm,args[1])
        })
    },
    text(node,expr,vm){
        //console.log("处理v-text指令的方法")
        //console.log(node)// "{{school.name}}"
        //console.log(expr)// {{school.name}}
        let fn=this.updater["textUpdater"]
        let content= expr.replace(/\{\{(.+?)\}\}/g,(...args)=>{
            //console.log(args[1])
            new Watcher(vm,args[1],()=>{
                fn(node,this.getContentValue(vm,expr));
            })
            return this.getVal(vm,args[1])
        })
        fn(node,content)
    },
    updater:{
        modelUpdater(node,value){
            node.value=value
        },
        htmlUpdater(){

        },
        textUpdater(node,value){
             node.textContent=value
        }
    }
}
//编译模板
class Compiler{
    constructor(el,vm){
        this.el=this.isElementNode(el)?el:document.querySelector(el)
        this.vm=vm;
        //console.log(this.vm);
        //console.log(this.el)现在拿到了el里面的元素节点
        //现在把真实的dom移动到文档碎片中,现在页面上已经没有dom结构了
        let fragment=this.node2fragment(this.el)
        //console.log(fragment)
         //在这里进行编译
        this.compile(fragment)
        this.el.appendChild(fragment)//调用这个方法将编译好的文档碎片重新渲染到页面
    }
    //判断一个属性是否为指令
    isDirective(attrName){
        return attrName.startsWith("v-")
    }

    //编译元素节点
    compileElement(node){
       //console.log(node.attributes)
    let attrs=node.attributes;//取出当前节点的属性,类数组
      [...attrs].forEach(attr=>{
           let{name,value:expr}=attr;
           //console.log(expr)//school.name
           if(this.isDirective(name)){
               //console.log(name)//v-model
               let [, directive]=name.split("-");
               //console.log(node)//<input type="text" v-model="school.name">
               //现在找到了模板中带有指令的节点
               //调用处理v-model的方法
               //console.log(this.vm)
                CompileUtil[directive](node,expr,this.vm);
           }
      })
    }
    //编译文本节点
    compileText(node){
         //获取所有文本节点中的内容
         let content=node.textContent
         //console.log(content)// 包括{{school.name}}等
        // 创建匹配 {{}} 的正则表达式
        let reg = /\{\{(.+?)\}\}/;
        if(reg.test(content)){
            //console.log(content)
            CompileUtil['text'](node,content,this.vm)
        }
    }
    //编译
    compile(node){
        //console.log(node.childNodes) 
        //[text, input, text, p, text, p, text, ul, text] 文本节点和元素节点
        let childNodes=node.childNodes;
        //转化为数组并循环判断每一个节点的类型
        [...childNodes].forEach(node => {
            if(this.isElementNode(node)){//是元素节点
                this.compile(node)//递归编译子节点
                this.compileElement(node)//编译元素节点的方法
            }else{//文本节点
                this.compileText(node)
            }
        });
    }
    node2fragment(node){
        //创建一个文档碎片 
        let fragment =document.createDocumentFragment();
        let firstChild;
        //循环取出根节点中的节点放入文档碎片
        while(firstChild=node.firstChild){
            fragment.appendChild(firstChild)
        }
        return fragment;
    }
    //判断一个节点是否是元素节点
    isElementNode(node){
        return node.nodeType===1
    }
}
//step-1先写Vue这个类
class Vue {
    //构造器
    constructor(options){
        //把el 和data项挂载到Vue实例上
        this.$el=options.el
        this.$data=options.data
        //if有el这个挂载项就开始编译
        if(this.$el){
            new Observer(this.$data)
            //console.log(this.$data)
            new Compiler(this.$el,this)
            this.proxyVm(this.$data)
            //第二个参数指实例本身
        }
    }
    //让vm代理data vue中使用vm实例代理了data vm.$data.xx => vm.xx
    proxyVm(data){
        for(let key in data){
            //console.log(key+"---"+data[key]+"-----"+data)
             Object.defineProperty(this,key,{
                 get(){
                     return data[key]
                 }
             })
        }
    }
}	
```

