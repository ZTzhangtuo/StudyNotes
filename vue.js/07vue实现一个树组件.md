#### 封装一个树组件

```js
//index.vue
<template>
  <div>
    <ul>
      <Item :model="treeData"></Item>
    </ul>
  </div>
</template>
<script>
import Item from "./Item";
export default {
  name: "app",
  data() {
    return {
        //树状结构数据
      treeData: {
        title: "Web全栈架构师",
        children: [
          {
            title: "Java架构师"
          },
          {
            title: "JS高级",
            children: [
              {
                title: "ES6"
              },

              {
                title: "动效"
              }
            ]
          },
          {
            title: "Web全栈",
            children: [
              {
                title: "Vue训练营",
                expand: true,
                children: [
                  {
                    title: "组件化"
                  },
                  {
                    title: "源码"
                  },
                  {
                    title: "docker部署"
                  }
                ]
              },
              {
                title: "React",
                children: [{ title: "JSX" }, { title: "虚拟DOM" }]
              },
              {
                title: "Node"
              }
            ]
          }
        ]
      }
    };
  },
  components: {
    Item
  }
};
</script>

```

```js
//Item.vue
<template>
  <li>
    <div @click="taggle">
      {{model.title}}
      <span v-if="isFolder">{{open?'-':'+'}}</span>
    </div>
    <ul v-show="open" v-if="isFolder">
        //递归组件
      <item class="item" v-for="model in model.children" :model="model" 
      :key="model.title"></item>
    </ul>
  </li>
</template>
<script>
export default {
  name: "Item",
  props: {
    model: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      open: false
    };
  },
  computed: {
    isFolder() {
      return this.model.children && this.model.children.length;
    }
  },
  methods: {
    taggle() {
      this.open = !this.open;
    }
  }
};
</script>
```

效果如下:

![1565773791241](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1565773791241.png)