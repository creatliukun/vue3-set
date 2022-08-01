# vue3-study

## 安装以来项

```
yarn install
```

### 运行文件

```
yarn serve
```

### 默认情况下，provide/inject 绑定并不是响应式的

```
可以通过传递一个 ref property 或 reactive 对象给 provide 来改变这种行为

我们也可以在组合式 API 中使用 provide/inject。两者都只能在当前活动实例的 setup() 期间调用。
使用组合式 API 为 MyMarker 组件提供用户的位置
按照打印顺序： provide--> computed --->created
watch: 需要所监听的元素改变后才会触发，所以刚开始的时候不会触发watch，触发watch时执行的是beforeUpdate和updated这两个生命钩子函数，如果响应式数据发生了变化，子组件依赖了此数据，则会执行生命周期函数的更新

在 setup() 中使用 provide 时，我们首先从 vue 显式导入 provide 方法。这使我们能够调用 provide 来定义每个 property

setup中的provide和单独的provide可以混用，是不是意味着setup中其他的元素也可以和vue2中的一起使用呢？
而且外面的元素的优先级比setup中的优先级要高
```

## 动态组件 & 异步组件

### 在动态组件上使用 keep-alive

我们之前曾经在一个多标签的界面中使用 is attribute 来切换不同的组件：

```jsx
<component :is="currentTabComponent"></component>
// <!-- 失活的组件将会被缓存！-->
<keep-alive>
  <component :is="currentTabComponent"></component>
</keep-alive>
```

### 异步组件

在大型应用中，我们可能需要将应用分割成小一些的代码块，并且只在需要的时候才从服务器加载一个模块。为了实现这个效果，Vue 有一个 defineAsyncComponent 方法：

```js
const { createApp, defineAsyncComponent } = Vue;

const app = createApp({});

const AsyncComp = defineAsyncComponent(
  () =>
    new Promise((resolve, reject) => {
      resolve({
        template: "<div>I am async!</div>",
      });
    })
);

app.component("async-example", AsyncComp);
```

### 模板应用

尽管存在 prop 和事件，但有时你可能仍然需要在 JavaScript 中直接访问子组件。为此，可以使用 ref attribute 为子组件或 HTML 元素指定引用 ID。

```js
<base-input ref="usernameInput"></base-input>;

this.$refs.usernameInput.focusInput();
```

```md
$refs 只会在组件渲染完成之后生效。也就是 mounted 钩子函数之后才可以有效。这仅作为一个用于直接操作子元素的“逃生舱”——你应该避免在模板或计算属性中访问 $refs。计算属性的执行是在 created 之前，对 data 进行处理，如果修改值之后的话，计算属性会重新计算，但是初始化的时候，计算熟悉还是在前面执行的
```

## 组合式 API

将同一个逻辑关注点相关代码收集在一起会更好
使用组合式 API，我们首先需要一个可以实际使用它的地方。在 Vue 组件中，我们将此位置称为 setup

```md
在 setup 中你应该避免使用 this，因为它不会找到组件实例。setup 的调用发生在 data property、computed property 或 methods 被解析之前，所以它们无法在 setup 中被获取
setup 选项是一个接收 props 和 context 的函数.此外， setup 返回的所有内容都暴露给组件的其余部分 (计算属性、方法、生命周期钩子等等) 以及组件的模板
```

```js
export default {
  components: { RepositoriesFilters, RepositoriesSortBy, RepositoriesList },
  props: {
    user: {
      type: String,
      required: true,
    },
  },
  setup(props) {
    console.log(props); // { user: '' }

    return {}; // 这里返回的任何内容都可以用于组件的其余部分
  },
  // 组件的“其余部分”
};
```

在 Vue 3.0 中，我们可以通过一个新的 ref 函数使任何响应式变量在任何地方起作用,ref 为我们的值创建了一个响应式引用。在整个组合式 API 中会经常使用引用的概念

### 在 setup 内注册生命周期钩子

组合式 API 上的生命周期钩子与选项式 API 的名称相同，但前缀为 on：即 mounted 看起来会像 onMounted

```jsx
import { fetchUserRepositories } from '@/api/repositories'
import { ref, onMounted } from 'vue'

// 组件中
setup (props) {
    // 通过ref实现数据的响应式更新
  const repositories = ref([])
  const getUserRepositories = async () => {
    repositories.value = await fetchUserRepositories(props.user)
  }
  // onMounted和普通的钩子函数mounted类似
  onMounted(getUserRepositories) // 在 `mounted` 时调用 `getUserRepositories`
  // 如果 setup 返回一个对象，那么该对象的 property 以及传递给 setup 的 props 参数中的 property 就都可以在模板中访问到：
  return {
    // 注意，从 setup 返回的 refs 在模板中访问时是被自动浅解包的，因此不应在模板中使用 .value
    repositories,
    getUserRepositories
  }
}
```

### watch 响应式更改

```js
import { ref, watch } from "vue";

const counter = ref(0);
// watch接受三个参数
// 1.counter：想要侦听的响应式引用或 getter 函数
// 2.回调函数
// 3.可选的配置选项
watch(counter, (newValue, oldValue) => {
  console.log("The new counter value is: " + counter.value);
});
```

### 独立的 computed 属性

与 ref 和 watch 类似，也可以使用从 Vue 导入的 computed 函数在 Vue 组件外部创建计算属性

```js
import { ref, computed } from "vue";

const counter = ref(0);
const twiceTheCounter = computed(() => counter.value * 2);

counter.value++;
console.log(counter.value); // 1
console.log(twiceTheCounter.value); // 2
```

## Setup 参考

使用 setup 函数时，它将接收两个参数：

- 1，props
- 2，context

### 参数 props

```js
export default {
  props: {
    title: String,
  },
  // setup 函数中的 props 是响应式的，当传入新的 prop 时，它将被更新
  // props 是响应式的，不能使用 ES6 解构，否则就会消除 prop 的响应性
  setup(props) {
    // 1，props默认不能结构，如果需要解构 prop，可以在 setup 函数中使用 toRefs 函数来完成此操作：
    const { title } = toRefs(props);
    console.log(title.value);
  },
};
```

当 title 是可选参数的时候，用户没有传入 title 时，toRefs 将不会为 title 创建一个 ref。这时可以用 toRef 进行替代.没有值得时候，创建了 title 这个 ref，他的创建的的\_defaultValue 值是 undefined。当有值的时候就返回传递的值

```js
import { toRef } from 'vue'
setup(props) {
  const title = toRef(props, 'title')
  console.log(title.value)
}
```

### 参数 Context

传递给 setup 函数的第二个参数是 context。context 是一个普通 JavaScript 对象，暴露了其它可能在 setup 中有用的值：

```js
export default {
  // context 是一个普通的 JavaScript 对象，也就是说，它不是响应式的，这意味着你可以安全地对 context 使用 ES6 解构
  //   setup(props, context) {
  setup(props, { attrs, slots, emit, expose }) {
    // Attribute (非响应式对象，等同于 $attrs)
    // console.log(context.attrs);
    // 插槽 (非响应式对象，等同于 $slots)
    // console.log(context.slots);
    // 触发事件 (方法，等同于 $emit)
    // console.log(context.emit);
    // 暴露公共 property (函数)
    // console.log(context.expose);
  },
};
```

<p style="font-size:14px">
attrs 和 slots 是有状态的对象，它们总是会随组件本身的更新而更新。这意味着你应该避免对它们进行解构，并始终以 attrs.x 或 slots.x 的方式引用 property。请注意，与 props 不同，attrs 和 slots 的 property 是非响应式的。如果你打算根据 attrs 或 slots 的更改应用副作用，那么应该在 onBeforeUpdate 生命周期钩子中执行此操作
</p>

### 访问组件的 property

```js
// 可以访问的property
props
attrs
slots
emit

// 将无法访问以下组件选项：
data
computed
methods
refs (模板 ref)
```

### 使用渲染函数

setup 还可以返回一个渲染函数，该函数可以直接使用在同一作用域中声明的响应式状态

```js
// import { h, ref, reactive } from "vue";

// export default {
//   setup() {
//     const readersNumber = ref(0);
//     const book = reactive({ title: "Vue 3 Guide" });
//     // 请注意这里我们需要显式使用 ref 的 value
//     return () => h("div", [readersNumber.value, book.title]);
//   },
// };
```

将这个组件的方法通过模板 ref 暴露给父组件时可以通过调用 expose 来解决这个问题，给它传递一个对象，其中定义的 property 将可以被外部组件实例访问 ?

```js
// import { h, ref } from "vue";
// export default {
//   setup(props, { expose }) {
//     const count = ref(0);
//     const increment = () => ++count.value;

//     expose({
//       // increment 方法现在将可以通过父组件的模板 ref 访问
//       increment,
//     });

//     return () => h("div", count.value);
//   },
// };
```

### 使用 this
在 setup() 内部，this 不是该活跃实例的引用，因为 setup() 是在解析其它组件选项之前被调用的，所以 setup() 内部的 this 的行为与其它选项中的 this 完全不同。这使得 setup() 在和其它选项式 API 一起使用时可能会导致混淆
