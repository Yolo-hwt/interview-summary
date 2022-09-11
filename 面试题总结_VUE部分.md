# Vue部分

## 为什么vue的数据data要用return方式返回

不使用return包裹的数据会在项目的全局可见，会造成变量污染；

而使用return包裹后数据中变量只在当前组件中生效，不会影响其他组件。

**官方：**
当一个组件被定义， data 必须声明为返回一个初始数据对象的函数，因为组件可能被用来创建多个实例。

如果 data仍然是一个纯粹的对象，则*所有的实例将共享引用同一个数据对象*！

过提供 data 函数，每次创建一个新实例后，我们能够调用 data函数，从而**返回初始数据的一个全新副本数据对象**。

## vue响应式原理

是Vue的核心特性之一，**数据驱动视图**，我们**修改数据视图随之响应更新**

- 当 一 个 Vue 实 例 创 建 时 ， Vue 会 **遍 历 data 中 的 属 性** ， 用 **Object.defineProperty将 它 们 转 为 getter/setter**

- 每个组件实例都对应一个 **watcher** 实例，它会在组件渲染的过程中**把“接触”过的数据 property 记录为依赖**。之后当**依赖项的 setter 触发时，会通知 watcher**，从而使它**关联的组件重新渲染。**

![data](面试题总结_VUE部分.assets/data.png)

**实现：**

数据劫持+发布订阅

(数据劫持就是Object.defineProperty)

1、实现一个监听器 `Observer` ，用来劫持并监听所有属性，如果属性发生变化，就通知订阅者；

2、实现一个订阅器 `Dep`，用来收集订阅者，对监听器 `Observer` 和 订阅者 `Watcher` 进行统一管理；

3、实现一个订阅者 `Watcher`，可以收到属性的变化通知并执行相应的方法，从而更新视图；

4、实现一个解析器 `Compile`，可以解析每个节点的相关指令，对模板数据和订阅器进行初始化。

![3.png](面试题总结_VUE部分.assets/16c4a3ce0bcb0d91tplv-t2oaga2asx-zoom-in-crop-mark3024000.webp)

**关键理解**

- 在 obsever 中使用Dep，在 **get** 中添加 **Dep.target** (观察者)，在 **set** 中 触发 **notify** (通知)

  - ```javascript
    // 获取值
    get() {
        // 在这里添加观察者对象 Dep.target 表示观察者
        Dep.target && dep.addSub(Dep.target)
        return value
    },
     // 设置值
        set(newValue) {
            if (newValue === value) return
            value = newValue
            self.walk(newValue)
            // 触发通知 更新视图
            dep.notify()
        },  
    ```

    

- 由**dep收集依赖**，dep中会有一个sub数组用于存储收集到的观察者（收集依赖）

  - ```javascript
    class Dep {
      constructor() {
        // 存储观察者
        this.subs = []
      }
      // 添加观察者
      addSub(sub) {
        // 判断观察者是否存在 和 是否拥有update方法
        if (sub && sub.update) {
          this.subs.push(sub)
        }
      }
      // 通知方法
      notify() {
        // 触发每个观察者的更新方法
        this.subs.forEach((sub) => {
          sub.update()
        })
      }
    }
    ```

    

- 当**Observer发现响应式属性在setter中更新**，会**调用dep的notify方法**来通知之前收集依赖中的所有相关watcher

- **wathcer调用update实现视图更新**（watcher在compile编译阶段被创建）



## vue双向数据绑定

！！！vue是响应式的，默认数据不是双向绑定的，但是提供了实现数据双向绑定的手段，我们可以通过v-model的语法糖来实现

`MVVM` 数据双向绑定主要是指：**数据变化更新视图，视图变化更新数据**，如下图所示：

![2.png](面试题总结_VUE部分.assets/16c4a3ce0c045693tplv-t2oaga2asx-zoom-in-crop-mark3024000.webp)

- 双向数据绑定通常是指我们使用的`v-model`指令的实现，是`Vue`的一个特性，也可以说是一个`input`事件和`value`的语法糖。
-  `Vue`通过`v-model`指令为组件添加上`input`事件处理（监听oninput方法）和`value`属性的赋值。

```javascript
<template>
   <!-- 这里添加了input时间的监听和value的属性绑定 -->
   <input @input='onInput' :value='localValue' />
   <span>{{localValue}}</span>
</template>
<script>
  export default{
    data(){
      return {
        localValue:'',
      }
    },
    methods:{
      onInput(v){
         //在input事件的处理函数中更新value的绑定值
         this.localValue=v.target.value;
         console.log(this.localValue)
      }
    }
  }
</script>
```



## vue对于对象，数组的响应式动态更新

！！！由于 JavaScript 的限制，Vue **不能检测**数组和对象的变化

 Vue2 会在**初始化实例时对 property 执行 getter/setter 转化**，对于自定义新增的数据无法实现响应式

**对象property更新**

```javascript
var vm = new Vue({
  data:{
    a:1
  }
})
// `vm.a` 是响应式的，因为在初始化实例时候将a执行了getter/setter转化
vm.b = 2
// `vm.b` 是非响应式的，直接在根元素上动态添加了属性，不允许
```

- 对于已经创建的实例，Vue **不允许动态添加根级别的响应式 property**。必须在初始化时侯在data中定义好

- 可以使用 `Vue.set(object, propertyName, value)` 方法向**嵌套对象添加响应式 property。**

  ```javascript
  Vue.set(vm.someObject, 'b', 2)
  //还可以使用 vm.$set 实例方法，这也是全局 Vue.set 方法的别名：
  this.$set(this.someObject,'b',2)
  ```

- 为已有对象添加多个property，由于**直接添加在已有对象上的property无法实现响应式**，所以不能直接使用object.assgin()，**需整合创建为一个新对象**

  ```javascript
  // 代替 `Object.assign(this.someObject, { a: 1, b: 2 })`
  this.someObject = Object.assign({}, this.someObject, { a: 1, b: 2 })
  ```



**数组元素更新**

**Vue 不能检测以下数组的变动：**

1. 当你**利用索引直接设置一个数组项**时，例如：`vm.items[indexOfItem] = newValue`
2. 当你**修改数组的长度**时，例如：`vm.items.length = newLength`

**解决**

1.使用数组方法更改数组

```javascript
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

2.使用Vue.set

```javascript
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
//vm.$set
vm.$set(vm.items, indexOfItem, newValue)
```



**原因：**

vue出于对性能的考虑，没有对数组元素做响应式，**可以做数组的响应式，但是太耗性能**

```javascript
//vue.js关于observer的部分源码
export class Observer {
  // ......
  constructor (value: any) {
    this.value = value
    this.dep = new Dep()
    this.vmCount = 0
    def(value, '__ob__', this)
    if (Array.isArray(value)) {
      // 这里对数组进行单独处理
      if (hasProto) {
        protoAugment(value, arrayMethods)
      } else {
        copyAugment(value, arrayMethods, arrayKeys)
      }
        //数组处理
      this.observeArray(value)
    } else {
      // 对对象遍历所有键值
      this.walk(value)
    }
  }
  walk (obj: Object) {
    const keys = Object.keys(obj)
    for (let i = 0; i < keys.length; i++) {
      defineReactive(obj, keys[i])
    }
  }
  observeArray (items: Array<any>) {
    for (let i = 0, l = items.length; i < l; i++) {
      observe(items[i])
    }
  }
}
```

vue 对对象是采取 `Object.keys`然后 `defineReactive` 所有键值

而对数组并没这样做，而是**只 `observe` 了每个元素的值，数组的下标因为没有被监听**，所以直接通过下标修改值是不会更新视图的。

！！！而数组方法能够响应式，是因为 Vue 对数组的方法进行了 `def` 操作

## vue异步更新

Vue 在更新 DOM 时是**异步**执行的。

- 只要**侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。**

- 如果同一个 **watcher 被多次触发，只会被推入到队列中一次**。在缓冲时**去除重复数据**以避免不必要的计算和 DOM 操作

- 在**下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作**。
- 所以如果想**操作更新后的dom就需要使用$nextTick(callback)在回调中操作**

**内部细节**

Vue 在内部对异步队列尝试使用原生的 `Promise.then`、`MutationObserver` 和 `setImmediate`

如果执行环境不支持，则会采用 `setTimeout(fn, 0)` 代替。

## vue组件通信

### 父子组件通信(9种方式)

#### **props（父传子）**

在父级组件中给子级组件添加自定义属性

```
<template>
...
<prop-test :msg="msgForPropTest" />
...
</template>
...
data() {
    return {
      msgForPropTest: "app给proptest组件的一封信",
    };
  },
...
```

然后再在子级组件中添加props配置接收（有三种方式接收）

```
//1数组
props:['msg']
//2对象	声明接收数据类型
props:[{msg:String}]
//3验证对象
props:{
     msg: {
     type: String, //指定值类型
     required: true, //是否为必传项
     default: "", //默认值，如没有父组件没有传递，则默认为空对象
     //一般required与default不一起使用
    },
}

```

**总结: **

**prop 只可以从上一级组件传递到下一级组件（父子组件），即所谓的单向数据流。而且 prop 只读，不可被修改，所有修改都会失效并警告。**

#### **自定义事件（子传父）**

父组件在子组件身上绑定自定义事件

```
...
<prop-test  @emitMsgToApp="emitMsgToApp" />
...
 methods: {
    emitMsgToApp(msg) {
      this.msgFromPropTest = msg;
    },
}

```

子组件使用**$emit**调用自定义事件

```
...
<button @click="sendMsgToApp" style="width=100px,height=50px">
      发送信息给app
    </button>
...
methods: {
    sendMsgToApp() {
      this.$emit("emitMsgToApp", this.msgToApp);
    },
  },
```

#### **parent&&children**（父子组件间通信，局限：不能用于非父子组件通信）

- **类型**：`Vue instance`

- **详细**：

  指定已创建的实例之父实例，在两者之间建立父子关系。子实例可以用 `this.$parent` 访问父实例，子实例被推入父实例的 `$children` 数组中。

  > 节制地使用 `$parent` 和 `$children` - 它们的主要目的是作为访问组件的应急方法。更推荐用 props 和 events 实现父子组件通信
  >
  > 要注意边界情况，如在`#app`上拿`$parent`得到的是`new Vue()`的实例，在这实例上再拿`$parent`得到的是`undefined`，而在最底层的子组件拿`$children`是个空数组。也要注意得到`$parent`和`$children`的值不一样，`$children` 的值是数组，而`$parent`是个对象

```
父组件操作子组件数据
//methods
changeChildrenData() {
      this.$children.forEach((child) => {
        if (child.childrenData) {
          child.childrenData = "children值被改变了";
        }
      });
    },
    
//子组件获取父组件数据
//可操作重写父组件数据
getParentData() {
      return this.$parent.parentData;
    },
```

#### **provide/inject**（父传子，可跨父子组件间通信）

`provide`/ `inject` 是`vue2.2.0`新增的api, 简单来说就是父组件中通过`provide`来提供变量, 然后再子组件中通过`inject`来注入变量。

> 注意: 这里不论子组件嵌套有多深, 只要调用了`inject` 那么就可以注入`provide`中的数据，而不局限于只能从当前父组件的props属性中回去数据

provide inject，但是这种方式，官方不推荐，因为**这个方法真的是太不好管控了**，比如说我在根组件provide了this，孙孙重孙组件去使用了this里面的一个变量，这时候很难去跟踪到这个变量的出处了，而且你也并不知道，项目中哪个组件有用到这个变量，有没有在其他组件中进行改变，所以这个api在项目中很少人使用，但是很多人拿来写组件用

```javascript
//父组件
provide: {
    demo: "来自APP的 provide demo ",
        //function无法正常传递
    funcdeom: () => {
      return "funcdemo";
    },
  }
//子组件
inject: {
    demo: {
      from: "demo",
    },
    func: {
      from: "funcdeom",
    },
  }
//可以将inject作为props或者data//这里以data为例
data() {
    return {
      appProvideDemo: this.demo,
      appProvideFunc: this.func,
    };
  }

```

![image-20220817223504258](面试题总结_VUE部分.assets/image-20220817223504258.png)

![image-20220817223429170](面试题总结_VUE部分.assets/image-20220817223429170.png)

#### **ref/refs**（父子组件通信）

在父组件中给子组件加上ref标记

```
...
<prop-test ref="proptestmsg"/>
...
```

然后可以在父组件中使用this.$refs.proptestmsg获取此组件

```
getPropTestData() {
      return this.$refs.proptestmsg.msgToApp;
    },
```

#### **事件总线（无限制）**

缺点是**碰到多人合作时，会导致代码的维护性较低，代码可读性也较低**

`eventBus` 又称为事件总线，在vue中可以使用它来作为沟通桥梁的概念, 就像是所有组件共用相同的事件中心，可以向该中心注册发送事件或接收事件， 所以组件都可以通知其他组件。

> eventBus也有不方便之处, 当项目较大,就容易造成难以维护的灾难

1.事件总线的安装可以直接在当前的vue实例上面安装（main.js）

```
new Vue({
  render: h => h(App),
  beforeCreate() {
    Vue.prototype.$bus = this;
  },
}).$mount('#app')
```

使用方法为	**this.$bus**

2.新建一个js文件在其中创建一个vue实例用来充当事件总线（event-bus.js）

```
import Vue from 'vue'
export const EventBus = new Vue();
```

使用方法为先引入EventBus再使用，可以全局挂载，也可以局部使用

```
//全局挂载，类似于1方法只不过bus=EventBus这个新实例
import { EventBus } from "@/event-bus";
new Vue({
  render: h => h(App),
  beforeCreate() {
    Vue.prototype.$bus = EventBus;
  },
}).$mount('#app')
//这样全局挂载后可以使用this.$bus

按需局部挂载
即哪个组件要用就在哪挂载
```

**事件总线总体使用实例**

```
//app.vue中挂载方法
  mounted() {
   //第一种或者EventBus全局挂载
    this.$bus.$on("changeAppData", this.changeAppData);
    //引入的新建vue实例作为事件总线
    // EventBus.$on("changeAppData", this.changeAppData);
  },
   methods: {
    changeAppData(msg) {
      this.parentData = msg;
    }
  }
//子组件调用
changeParentData() {
      this.$bus.$emit("changeAppData", "proptest组件改变了App组件的值");
      // EventBus.$emit(
      //   "changeAppData",
      //   "proptest组件通过EventBus改变了App组件的值"
      // );
    },
```

#### **attr&&listener**

在vue2.4中，为了解决跨组件通信，引入了`$attrs` 和`$listeners`，

- `$attrs`  用于父组件传递数据给子组件

- `$listeners `用于子组件传递数据到父组件

新增了`inheritAttrs` 选项。

- **$attr**（父传子）
- 相当于父组件将数据流往下走，需要的子组件就自己用props捕获，剩下的由$attrs管理

 **在版本2.4以前**，默认情况下父作用域的不被认作props的属性百年孤独，将会“回退”且作为普通的HTML特性应用在子组件的根元素上。

```
//父组件
<template>
  <div>
    <child-dom :foo="foo" :bar="bar"></child-dom>
  </div>
</template>
//子组件
 props: ["foo"]
```

剩下的bar就会被退在子组件根元素上

![1631864762(../../../个人项目/尚品会/doc/技术点.assets/59f443043bd84e5d8d048367312ac1b0tplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp).jpg](技术点.assets/59f443043bd84e5d8d048367312ac1b0tplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

可以利用`inheritAttrs`选项控制，默认值为true即允许默认回退行为, 

将`inheritAttrs`的值设为**false**, 这些默认的行为会禁止掉。然后通过实例属性 `$attrs` ,可以将这些特性生效，且可以通过`v-bind` 绑定到子组件的非根元素上。

```
//父组件
<attr-test
      :age="attr_age"
      :name="attr_name"
      :phone="attr_phone"
    ></attr-test>
    
//AttrTest子组件内
<template>
  <div>
    <h2>这是attrtest组件</h2>
    <p>{{ $attrs }}</p>
  </div>
</template>
...
inheritAttrs: false, //禁止默认回退
props: ["age", "name"]
```

![image-20220818132501571](面试题总结_VUE部分.assets/image-20220818132501571.png)

![image-20220818132707383](面试题总结_VUE部分.assets/image-20220818132707383.png)

**未使用props声明的phone存在了$attrs中**

attrs还可以继续传递给下一子组件

```
//在attr-test组件中引入attr-test-child作为子组件
//为子组件绑定$attrs
<attr-test-child v-bind="$attrs"></attr-test-child>

//在attr-test-child中使用data接收
data() {
    return {
      attrsFromAttrTest: this.$attrs,
    };
  }
```

![image-20220818133509530](面试题总结_VUE部分.assets/image-20220818133509530.png)

```
或者也可以用props接收
props: {
    phone: {
      type: String,
      default() {
        return this.$attrs.phone;
      },
    },
  }
```

！！！props接收之后attrs就不复存在了，props接收数据的优先级高于data，这时候如果再用data接收一次就取不到数据

![image-20220818134408002](面试题总结_VUE部分.assets/image-20220818134408002.png)

- **$listeners**（子组件传递数据到父组件）
- 本质是父组件准备自定义方法，然后由$listeners传递给子组件，子组件emit触发事件

```
//app组件内
<attr-test
      @updateName="changeAttr_name"
      @updateAge="changeAttr_age"
></attr-test>

//attr-test组件内
//$listeners传递
<attr-test-child v-bind="$attrs" v-on="$listeners"></attr-test-child>

//attr-test-child组件内
//emit调用
changeAppAttrName() {
      this.$emit("updateName", "黄睿奇");
    },
```

#### 父子组件双向数据绑定

**利用v-model双向数据绑定+props传值实现**

这里写一个小demo，点击子组件内容同时+1父子组件绑定的值

1.在父组件app中定义一个data{...clickValue=0,}

2.使用v-model为子组件addone绑定clickValue

```
  <add-one v-model="clickValue"></add-one>
```

此时在子组件身上就了一个**$attr属性value**，且**value=父组件中的clickValue**

3.在子组件中使用**props接收value**（由于是v-model绑定的所以固定以value声明），额外定义一个localValue来存储并同步父组件的value值

```
props: {
    value: {
      type: Number,
      default: 0,
    },
  },
data(){
localValue:0
},
//为了创建后同步
watch: {
    value(v) {
    this.localvalue = v;
    },
},
create(){
//为了创建时同步父组件的value值
this.localValue=this.value
}
```

4.子组件在模板中显示value并为相应dom添加点击事件

```vue
<template>
  <div @click="addCount">双向数据绑定测试 click {{ value }}</div>
</template>
...
methods: {
    addCount() {
      this.localvalue++;
      //父组件中给子组件添加v-model双向数据绑定时添加了input事件处理
      //使用emit通知父组件更新
      this.$emit("input", this.localvalue);
    },
  },
```

click事件处理将localValue+1并$emit通知父组件更新

## vue设计一个弹窗组件

**基本思路**

1.定义弹窗模板

```vue
<template>
  <transition name="tipMove">
    <div class="tipBox" v-if="isShowTip">
      {{ message }}
    </div>
  </transition>
</template>
//定义动画，css部分省略，主要就是fixed定位，采取less语法
```

2.props传值

```js
message: "app手动创建的tipBox",
isShowTip: true,//是否显示
isAuto: true,//是否自动关闭
duringTime:2000//显示时间
```

3.自定义事件控制关闭

```js
closeTip() {
      if (!this.autoClock) {
        this.autoClock = setTimeout(() => {
          this.$emit("update:isShowTip", false);//通知父组件修改props值，可以利用update
          clearTimeout(this.autoClock);//清除计时器好习惯
          this.autoClock = null;
        }, this.duringTime + 250);//250为动画的过渡时间
      }
    },

//对应的父组件写法
<tip-box 
:isShowTip="tipIsShow"
@update:isShowTip="(val) => (tipIsShow = val)"
></tip-box>   

//.sync修饰符是@update:自定义属性名的语法糖
<tip-box :isShowTip.sync="tipIsShow"></tip-box>        
```

**组件方式**

据上，来构建vue文件，在父组件中引入并使用

```js
<tip-box
      :message="tipMsg"
      :isShowTip="tipIsShow"
      @update:isShowTip="(val) => (tipIsShow = val)"
      :isAuto="tipAutoControl"
      :duringTime="tipDuring"
    ></tip-box>   
```

**函数方式**

定义tipTool.js

1.Vue.extends()创建子类构造器

2.利用构造器创建实例并挂载

3.绑定自定义事件（原先是由父组件绑定然后子组件触发，改为函数创建方式需要子组件内部处理）

4.domAPI将组件根元素插入文档中

```js
export const createTipBox = (propsData) => {
    //Vue.extend创建组件构造器
    const Ctor = Vue.extend(TipBox)
    //创建组件实例并挂载
    const tipvm = new Ctor({ propsData }).$mount()
    //组件方式中由外层调用的父级组件提供props数据，故由父级控制数据变化进而控制弹窗
    //这里是函数式的创建构造器，由外部普通变量提供参数，所以需要组件自己监听
    tipvm.$on('update:isShowTip', (flag) => {
        tipvm.isShowTip = flag;
    })
    //通过原生dom API的方式将组件根元素插入文档
    document.body.appendChild(tipvm.$el)
    return tipvm;
}
```

**插件方式**

插件方式可以将上面两种方式功能集成在一起

1.src目录下创建plugins文件夹/index.js

2.引入刚才的组件和函数js文件，install方法安装插件

```js
import TipBox from '@/components/TipBox/index.vue'
import { createTipBox } from '@/components/TipBox/tipbox'

export default {
    install(Vue) {
        Vue.component(TipBox.name, TipBox)
        Vue.prototype.$myTipBox = createTipBox;
    }
}
```

3.在main.js中引入并结合Vue.use使用插件

## MVVM、MVC、MVP的区别

- **MVC**

MVC 通过分离 Model、View 和 Controller 的方式来组织代码结构。

当用户与页面产生交互的时候，Controller 中的事件触发器调用 Model 层来完成对 Model 的修改，然后 Model 层再去通知 View 层更新。

<img src="面试题总结_VUE部分.assets/image-20220908085154829.png" alt="image-20220908085154829" style="zoom: 67%;" />

其中 View 负责**页面的显示逻辑**

Model 负责**存储页面的业务数据， 以及对相应数据的操作**，**View 和 Model 应用了观察者模式**（view订阅model的更新）

Controller 层是 View 层和 Model 层的纽带，它主要负责**用户与应 用的响应操作。** 

**！！！注意**：

- 业务逻辑被放置在model层，能够更好的复用和修改增加业务
- View 层和 Model 层耦合在一起， 当项目逻辑变得复杂的时候，可能会造成代码的混乱，并且可能会对 代码的复用性造成一些问题

- **MVP**

MVC 中的 Controller 只知道 Model 的接口，因此它没有办法控制 View 层的更新，

MVP 模式中， View 层的接口暴露给了 Presenter 因此可以**在 Presenter 中将 Model 的变化和 View 的变化绑定在一起，以此来实现 View 和 Model 的同步更新**。

这样就**实现了对 View 和 Model 的解耦**， Presenter 还包含了其他的响应逻辑

<img src="面试题总结_VUE部分.assets/image-20220908100218296.png" alt="image-20220908100218296" style="zoom:67%;" />



- **MVVM**

**MVVM 分为 Model、View、ViewModel**

 Model 代表数据模型，**数据和业务逻辑都在 Model 层中定义**；

 View 代表 UI 视图，负责**数据的展示；** 

ViewModel 负责**监听 Model 中数据的改变并且控制视图的更新，处理用户交互操作**

<img src="面试题总结_VUE部分.assets/image-20220908094332715.png" alt="image-20220908094332715" style="zoom:67%;" />

**view 和 ViewModel 之间有着双向数据绑定的联系。**

因此当 Model 中 的数据改变时会触发 View 层的刷新，View 中由于用户交互操作而改 变的数据也会在 Model 中同步

**好处：**

这种模式实现了 **Model 和 View 的数据自动同步，因此开发者只需要 专注于数据的维护操作即可，而不需要自己操作 DOM**

**1. 低耦合**。视图（View）可以独立于Model变化和修改，一个ViewModel可以绑定到不同的"View"上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。

**2. 可重用性**。你可以把一些视图逻辑放在一个ViewModel里面，让很多view重用这段视图逻辑。

**3. 独立开发**。开发人员可以专注于业务逻辑和数据的开发（ViewModel），设计人员可以专注于页面设计，

**4. 可测试**。界面素来是比较难于测试的，测试可以针对ViewModel来写

**5.自动更新dom**。利用双向绑定，数据更新后视图⾃动更新,让开发者从 繁琐的⼿动 dom

**缺点：**

**Bug 很难被调试**

因为使⽤双向绑定的模式，当你看到界⾯异常了， **有可能是你 View 的代码有 Bug，也可能是 Model 的代码有问题。**

数 据绑定使得⼀个位置的 **Bug 被快速传递到别的位置**，要定位原始出问 题的地⽅就变得不那么容易了。

数据绑定的声明是**指令式地写 在 View 的模版当中的，这些内容是没办法去打断点 debug 的**

**内存限制**

对于⼤型的图形应⽤程序，**视图状态较多，model 也会很⼤**，⻓期持有，**不释放内存就造成了大量内存开销**，ViewModel 的构建和维护 的成本都会⽐较⾼

## slot是什么？有什么作用？原理是什么？

**slot（插槽），是vue的内容分发机制**

组件内部的模板引擎使用 slot 元素作为**承载分发内容的出口**。

插槽 **slot 是子组件的一个模板 标签元素**，元素是否显示，以及怎么显示是由父组件决 定的。

slot 又分三类，**默认插槽，具名插槽和作用域插槽。**

**插槽内部可以放任意内容，包括组件**

- **默认插槽**

当 slot 没有指定 name 属性值的时候一个 默认显示插槽，一个组件内只能有一个匿名插槽default

```vue
//组件：slot-demo
<template>
    <div>
        <slot>默认插槽内容</slot>
    </div>
</template>
//使用插槽
<slot-demo>此处填入插槽内容</slot-demo>
```

- **具名插槽**

带有具体名字的插槽，也就是带有 name 属性的 slot，一 个组件可以出现多个具名插槽。

定义：

组件内部使用<slot>标签加上name属性绑定组件内插槽具体名称（v-slot自能定义在template上）

使用：

使用组件插槽定义template并配合v-slot：name指定插槽名称

特殊：

默认插槽使用具名插槽

<template v-slot:default>

```vue
//组件
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
//组件插槽使用
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>
  <template v-slot:footer>
    <p>Here's some contact info</p> 
  </template>
</base-layout>
```

**具名插槽缩写**

**v-slot:替换为#**

```vue
<template #header>...</template>
<template v=slot:header>...</template>
```



- **作用域插槽**

```vue
//插槽定义
<span>
  <slot>{{ user.lastName }}</slot>
</span>
//插槽使用
//！！！！！此时在父级作用域不能拿到子级的user
<current-user>
  {{ user.firstName }}
</current-user>
```

作用域插槽使得**插槽内容能够访问子组件中才有的数据**，

为了让 `user` 在父级的插槽内容中可用，我们可以将 `user` 作为 `<slot>` 元素的一个 **attribute** 绑定上去：

绑而此时绑定在 `<slot>` 元素上的 attribute 被称为**插槽 prop**

```vue
<span>
  <slot v-bind:user="user">//绑定user为插槽prop
    {{ user.lastName }}
  </slot>
</span>
//再次在父组件中使用
<current-user>
  <template v-slot:default="slotProps">//父组件使用v-slot声明获取插槽prop
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

- **动态插槽**

```vue
  //dynamicSlotName为父组件的一个data
  <template v-slot:[dynamicSlotName]>
  ...
  </template>
```

**父组件获取插槽prop的方式**

1.独占默认插槽（默认插槽）

```vue
<template v-slot:default="slotProps">//如获取user：slotProps.user
//缩写
//不带参数的 v-slot 被假定对应默认插槽
<template v-slot="slotProps">
```

默认插槽的缩写语法**不能**和具名插槽混用，因为它会导致作用域不明确：

```vue
<!-- 无效，会导致警告 -->
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
  <template v-slot:other="otherSlotProps">
    slotProps 在这里不能被使用
  </template>
</current-user>
```

2.具名插槽prop

```vue
 <template v-slot:other="otherSlotProps">
```

3.解构插槽prop

插槽prop支持es5解构语法

```vue
 <template v-slot:other="{user}">
 //将解构出来的user重命名为person
  <template v-slot:other="{user:person}">
```



## $nextTick原理及作用

引入异步更新队列机制的原因：

如果是同步更新，则多次对一个或多个属性赋值，会频繁触发 UI/DOM 的渲染，异步更新可以减少一些无用渲染

**Vue 的 nextTick 其本质是对 JavaScript 执行原理 EventLoop 的 一种应用**

核 心 是 利 用 了 如 Promise 、 MutationObserver 、 setImmediate、setTimeout 的**原生 JavaScript 方法来模拟对应的 微/宏任务的实现**

本质是为了**利用 JavaScript 的这些异步回调任务队列来实现 Vue 框架中自己的异步回调队**

nextTick满足实际应用中对 DOM 更新数据时机的后续逻辑处理 

**使用nextTick的场景**

1.在数据变化后执行的某个操作，而这个操作**需要使用随数据变化而变 化的 DOM 结构**的时候，这个操作就需要方法在 nextTick()的回调函 数中。

2.如果在 created()钩子进行 DOM 操作，也一定要 放在 nextTick()的回调函数中。

 因为在 created()钩子函数中，**页面的 DOM 还未渲染，这时候也没办 法操作 DOM**，所以，此时如果想要操作 DOM，必须将操作的代码放在 nextTick()的回调函数中。

## Vue单页应用与多页应用的区别

- SPA

**SPA 单页面应用（SinglePage Web Application）**

指只有一个主页 面的应用，一开始**只需要加载一次 js、css 等相关资源。所有内容都 包含在主页面**，对每一个功能模块组件化。单页应用跳转，就是切换 相关组件，**仅仅刷新局部资源**

- MPA

**MPA 多页面应用 （MultiPage Application）**

指有多个独立页面的 应用，**每个页面必须重复加载 js、css 等相关资源**。多页应用跳转， **需要整页资源刷新**

- 区别

![image-20220908133002369](面试题总结_VUE部分.assets/image-20220908133002369.png)

## Vue 中封装的数组方法有哪些，其如何实现页面更新

在vue中使用Object.defineProperty对数据进行数据劫持，但是此方法**并不能监听到数组内部的变化，数组长度变化，数组截取值变化**等，所以需要对这些操作进行**hack**让Vue能够监听到其中的变化

- Vue.set() 方法利用索引为数组添加响应式元素：

```
Vue.set(Array, index, newValue) 或者 this.$set(Array, index, newValue)
```

- vue 将被侦听的数组的变异方法（**改变原数组的方法**）进行了包裹，所以他们也将会触发视图更新：

```
push()：向数组的末尾添加一个或多个元素
pop()：删除数组的最后一个元素
unshift()：向数组的开头添加一个或更多元素
shift()：删除数组的第一个元素
sort()：对数组的元素进行排序
reverse()：颠倒数组中元素的顺序
splice()：向/从数组中添加/删除项目，然后返回被删除的项目（Vue.set() 对于数组的处理其实就是调用了 vue 包裹后的 splice 方法）
```

**vue响应式数组方法原理**

![image-20220908133704466](面试题总结_VUE部分.assets/image-20220908133704466.png)

首先获取到这个数组 的--ob--，也就是它的 Observer 对象，如果有新的值，就调用 observeArray 继续对新的值观察变化，然后手动调用 notify， 通知渲染 watcher，执行 update

## Vue的data中的某一个属性值的变化，视图会立即同步执行重新渲染吗？

Vue 在更新 DOM 时是异步执行的。

只要侦听到数据变化， Vue 将开启一个队列， 并缓冲在同一事件循环中发生的所有数据变更。 

如果**同一个 watcher 被多次触发，只会被推入到队列中一次**。

这种在 缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。然后，**在下一个的事件循环 tick 中，Vue 刷新队列并执行实际 （已去重的）工作。**

## 子组件可以直接改变父组件的数据吗

子组件不可以直接改变父组件的数据。这样做主要是为了维护父子组 件的单向数据流。

如果这样做了，Vue 会在浏览器的控制台中 发出警告

**为什么建议更改？**

这是为了防止意外的改变父组件状态，使得应用的数据 流变得难以理解，导致数据流混乱。如果破坏了单向数据流，当应用 复杂时，debug 的成本会非常高。

**提倡的更改方式**

通过 **$emit** 派发一个自定义事件，父组件接收到后，由父组件 修改。

## vue的优点

- **轻量级框架**：只关注视图层，是一个构建数据的视图集合，大小只有 几十 kb 
- **简单易学**：国人开发，中文文档，不存在语言障碍 ，易于理解和学习
- **双向数据绑定**：保留了 angular 的特点，在数据操作方面更为简单
- **组件化**：保留了 react 的优点，实现了 html 的封装和重用，构 建单页面应用方面有着独特的优势；
- **视图，数据，结构分离**：使数据的更改更为简单，不需要进行逻辑代 码的修改，只需要操作数据就能完成相关操作
- **虚拟 DOM**：dom 操作是非常耗费性能的，不再使用原生的 dom 操作 节点，极大解放 dom 操作，但具体操作的还是 dom 不过是换了另一 种方式
- **运行速度更快**：相比较于 react 而言，同样是操作虚拟 dom，就性 能而言， vue 存在很大的优势

## assets和static的区别

**相同点：**assets 和 static 两个都是存放静态资源文件。项目中所 需要的资源文件图片，字体图标，样式文件等

**不同点：**

- assets 中存放的静态资源文件在项目打包时会**将 assets 中放置的静态资源文件进行打包（压缩体积）**
- **static 中放置的静态资源文件就不会要走打包 压缩格式化等流程**，而是直接进入打包好的目录
- static 中的资源文件由于没有进行压缩等操作，所以文件的体积也 就相对于 assets 中打包后的文件提交较大点。

**总结：**

项目需要的样式文件 js 文件等都可以放置在 assets 中，走打包这一流程。减少体积。

而项目中引入的第三方的 资源文件如 iconfoont.css 等文件可以放置在 static 中，因为这 些引入的第三方文件已经经过处理，不再需要处理，直接上传

## delete和Vue.delete删除数组的区别

- delete 只是被删除的元素变成了 **empty/undefined** 其他的元素的 **键值还是不变**。 
- Vue.delete **直接删除了数组 改变了数组的键值**。

## Vue模板编译原理

明确：vue中的**template**无法被浏览器解析并渲染，这并不是浏览器的标准，不是正确的HTML语法

vue模板编译目的：将template转化为浏览器可识别的HTML元素

过程：将 **template 转化成一 个 JavaScript 函数**，浏览器就可以**执行这一个函数并渲染出对 应的 HTML 元素**，让浏览器能够正确识别，这一个转化的过程，就称为模板编译。

1.解析parse

使用大量的**正则表达式对 template 字符串进行解析，将 标签、指令、属性等转化为抽象语法树 AST**

2.优化optimize

**遍历 AST，找到其中的一些静态节点并进行标记**，方便在 页面重渲染的时候进行 **diff 比较时，直接跳过这一些静态节点**，优 化 runtime 的性能

3.生成

将最终的 **AST 转化为 render 函数字符**

## v-if和v-for优先级

v-for 优先于 v-if 被解析

如果同时出现，每次渲染都会**先执行循 环再判断条件**，无论如何循环都不可避免，浪费了性能

如果条件出现在循环内部，可**通 过计算属性提前过滤掉那些不需要显示的项**。

## Vue组件化的理解

1.组件是**独立和可复用的代码组织单元**。是 Vue 核心特性之 一，开发者使用小型、独立和通常可复用的组件构建大型应用； 

2.组件化开发能**大幅提高应用开发效率、测试性、复用性**等；

3.组件使用按分类有：页面组件、业务组件、通用组件； 

4.vue 的组件是基于配置的，我们通常编写的组件是组件配置而非组 件，框架后续会生成其构造函数，它们基于 VueComponent，扩展于 Vue； 

5.vue 中常见组件化技术有：属性 prop，自定义事件，插槽等，它们 主要用于组件通信、扩展等；

6**.合理的划分组件，有助于提升应用性 能**；

7.组件应该是高内聚、低耦合的； 

8.遵循单向数据流的原则。

## Vue设计原则的理解

**1.渐进式 JavaScript 框架**

Vue 被设计 为可以自底向上逐层应用。

Vue 的核心库只关注视图层，不仅易于上 手，还便于与第三方库或既有项目整合。

当与现代化的工 具链以及各种支持类库结合使用时，Vue 也完全能够为复杂的单页应 用提供驱动。

**2.易用性**

vue 提供**数据响应式、声明式模板语法和基于配置的组件** 系统等核心特性。

我们只需要关注应用的核心业务即可，只要 会写 js、html 和 css 就能轻松编写 vue 应用。

**3.灵活性**

渐进式框架的最大优点就是灵活性，如果应用足够小，我 们可能仅需要 vue 核心特性即可完成功能；

随着应用规模不断扩大， 我们才可能逐渐引入路由、状态管理、vue-cli 等库和工具，**不管是 应用体积还是学习难度都是一个逐渐增加的平和曲线。** 

**4.高效性**

超快的虚拟 DOM 和 diff算法使我们的应用拥有最佳的性能 表现。

vue3 中引入 Proxy 对数据响应式 改进以及编译器中对于静态内容编译的改进都会让 vue 更加高效

## Vue的生命周期

每个 Vue 实例在被创建时都要经过一系列的初始化过程

——例如，需要设置**数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等**。

同时在这个过程中也会运行一些叫做**生命周期钩子**的函数，这给了用户在不同阶段添加自己的代码的机会

！！！注意点：

**不要在选项 property 或回调上使用箭头函数**，比如

```
created: () => console.log(this.a)
vm.$watch('a', newValue => this.myMethod())
```

因为箭头函数并没有 `this`，`this` 会作为变量一直向上级词法作用域查找，直至找到为止，

**！容易导致错误**

```
Uncaught TypeError: Cannot read property of undefined
Uncaught TypeError: this.myMethod is not a function
```

生命周期钩子

**1.beforeCreate（创建前）**：数据观测和初始化事件还未开始，此时 **data 的响应式追踪、event/watcher 都还没有被设置**，也就是说**不能访问到 data、computed、watch、methods 上的方法和数据。** 

**2.created（创建后）** ：实例创建完成，实例上配置的 options 包括 **data、computed、watch、methods 等都配置完成，但是此时渲染节点还未挂载到 DOM，所以不能访问到** el 属性。 

**3.beforeMount（挂载前）**：在挂载开始之前被调用，相关的 render 函数首次被调用。实例已完成以下的配置：**编译模板，把 data 里面 的数据和模板生成 html**。**此时还没有挂载 html 到页面上**。

**4.mounted（挂载后）**：在 el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用。实例已完成以下的配置：**用上面编译好的 html 内 容替换 el 属性指向的 DOM 对象**。完成模板中的 html 渲染到 html 页 面中。此过程中进行 ajax 交互。 

**5.beforeUpdate（更新前）**：响应式数据更新时调用，此时虽然**响应式数据更新了，但是对应的真实 DOM 还没有被渲染。** 

**6.updated（更新后）** ：在由于数据更改导致的虚拟 DOM 重新渲染和 打补丁之后调用。**此时 DOM 已经根据响应式数据的变化更新了**。调 用时，组件 DOM 已经更新，所以可以执行依赖于 DOM 的操作。然而 在大多数情况下，应该避免在此期间更改状态，因为这可能会导致更 新无限循环。该钩子在**服务器端渲染期间不被调用。** 

**7.beforeDestroy（销毁前）**：实例销毁之前调用。**这一步，实例仍 然完全可用，this 仍能获取到实例**。 

**8.destroyed（销毁后）**：实例销毁后调用，调用后，Vue 实例指示 的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例 也会被销毁。该钩子在**服务端渲染期间不被调用**

<img src="面试题总结_VUE部分.assets/image-20220908163103509.png" alt="image-20220908163103509" style="zoom:50%;" />

## 简述mixin、extends的覆盖逻辑



## Vue和React的区别