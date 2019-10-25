### Vue中数据劫持的实现原理

vue是通过数据劫持的方式来实现数据绑定的，最核心的方法是通过Object.defineProperty()来实现对属性的劫持，那么在设置或者获取的时候我们就可以在get或者set方法里加入其他的触发函数，达到监听数据变动的目的，无疑这个方法是本文中最重要、最基础的内容之一。

大致原理：

1. 定义一个监听函数，对对象的每一个属性进行监听

2. 通过Object.defineProperty方法对监听的每一个属性设置get和set方法

3. 对对象实行监听

4. 对对象内嵌对象的处理

5. 对数组对象进行处理

   

```js

// 定义一个对象
let obj = {
    name: 'tom',
    age: {
        old: 18
    }

}
// 定义一个监听函数
//如果是对象，就遍历，对每个属性定义get和set
function observer(obj) {
    if (typeof obj === 'object') {
        for (let key in obj) {
            //defineReactive方法设置get和set
            defineReactive(obj, key, obj[key]);
        }
    }
}
//defineReactive方法处理每一个属性
function defineReactive(obj, key, value) {
    // 如果对象的属性也是一个对象。迭代处理
    observer(value);
    Object.defineProperty(obj, key, {
        get() {
            console.log("get...")
            return value;
        },
        set(val) {
            console.log('数据更新了...')
            value = val;
        }
    })
}
//重写数组的方法
let arr = ['push', 'slice', 'shift', 'unshift'];
arr.forEach(method => {
    let oldPush = Array.prototype[method];
    Array.prototype[method] = function (value) {
        console.log("数据更新了")
        oldPush.call(this.value)
    }
})
    // observer(obj);
    // obj.name="jerry"

    // observer(obj);
    // obj.age.old=50
    // 输出为空

// 对对象的进行迭代处理后
    // observer(obj);
    // obj.age.old=50
    // 数据更新了

// 但是如果对象是一个数组，Object.defineProperty就不其作用了 例如
// obj.skill=[1,2,3];
// obj.skill.push(4);
// // 输出为空
//不止push slice shift ....都没作用


//再执行
// obj.skill=[1,2,3];
// obj.skill.push(4);
// //数据更新了。。。

observer(obj)
obj.age = [1,2,3]
obj.age.length--;
```



