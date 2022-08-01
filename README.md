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

```warning
$refs 只会在组件渲染完成之后生效。也就是mounted钩子函数之后才可以有效。这仅作为一个用于直接操作子元素的“逃生舱”——你应该避免在模板或计算属性中访问 $refs。
```
