<template v-if="isShowTemplate">
  <!-- <template> 元素上使用 v-if 条件渲染分组 -->
  <div>
    <!-- 在模板中处理数据  -->
    <div>{{ msg.split('').reverse().join('') }}</div>
    <!-- 使用防抖函数 -->
    <button @click="debouncedClick">打印123</button>
  </div>
  <!-- class 类名绑定的几种方法-->
  <div class="hello">
    <!-- 在组件类类名处理的各种方法 -->
    <div class="static" :class="{ active: isActive, 'text-danger': hasError }">
      +动态+静态方法结合
    </div>
    <div :class="classObject">+类名用对象表示</div>
    <div :class="computedClassObject">+用计算属性来绑定类名</div>
    <div :class="[activeClass, errorClass]">+用数组语法来绑定类名</div>
    <div :class="[isActive ? activeClass : '', errorClass]">
      +使用三元表达式,根据条件切换列表中的 class
    </div>
    <div :class="[{ active: isActive }, errorClass]">
      +数组语法中也可以使用对象语法
    </div>
  </div>
  <!-- 多个根组件中指定处理父组件传递接受的方法 $attrs.class-->
  <div :class="$attrs.class" class="son-class">
    多根组件绑定样式class,通过$attrs.class获取组件上的类名
  </div>
  <!-- 绑定内联样式 -->
  <div>
    <div :style="{ color: activeColor, fontSize: fontSize + 'px' }">
      -内联样式处理
    </div>
    <div :style="styleObject">-直接绑定到一个样式对象让模板更清晰</div>
    <div :style="[styleObject, styleObjectFont]">
      -数组语法将多个样式对象应用到同一个元素上
    </div>
    <!-- 数组+对象的写法 -->
    <div
      :style="[
        { display: ['-webkit-box', '-ms-flexbox', 'flex'] },
        styleObject,
        styleObjectFont
      ]"
    >
      -为 style 绑定中的 property
      提供一个包含多个值的数组，常用于提供多个带前缀的值，多重值
    </div>
  </div>
  <!-- 条件渲染 -->
  <div>
    <h5 v-if="isShow">条件渲染v-if</h5>
    <h5 v-else>条件渲染v-else</h5>
    <h4 v-show="isShow">
      条件渲染v-show是被css属性display:none处理了,元素还保留在页面里面
    </h4>
  </div>
  <!-- 组件 -->
  <div id="blog-posts-demo" :style="{ fontSize: postFontSize + 'em' }">
     <!-- 在组件上使用 v-model -->
    <DeComponentVue
      v-for="post in posts"
      :key="post.id"
      :title="post.title"
      v-model="comPSearchText"
      @enlarge-text="onEnlargeText"
    ></DeComponentVue>
    <!-- 简化后的做法 -->
    <!-- 组件上的v-model等价于  :model-value="comPSearchText"   @update:model-value="comPSearchText = $event" -->
    <!--  @enlarge-text="postFontSize += $event" -->

    <!-- 自定义事件也可以用于创建支持 v-model 的自定义输入组件。 -->
    <input v-model="searchText" />
    <!--v-model="searchText"等价于:value="searchText" @input="searchText = $event.target.value"  -->
    <input :value="searchText" @input="searchText = $event.target.value" />
  </div>
</template>

<script>
// 防抖和节流，为了使组件实例彼此独立，可以在生命周期钩子的 created 里添加防抖函数，移除组件时，取消防抖函数
import _ from 'lodash'
import DeComponentVue from './DeComponent.vue'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  components: {
    DeComponentVue
  },
  data () {
    return {
      isActive: true,
      hasError: true,
      error: null,
      classObject: {
        active: true,
        'text-danger': true
      },
      activeClass: 'active',
      errorClass: 'text-danger',
      activeColor: 'red',
      fontSize: 16,
      styleObject: {
        color: 'green'
      },
      styleObjectFont: {
        fontSize: '13px'
      },
      isShow: true,
      isShowTemplate: false,
      posts: [
        { id: 1, title: '父组件进行遍历' },
        { id: 2, title: '进入到子组件中' },
        { id: 3, title: '可以的' }
      ],
      postFontSize: 1,
      searchText: '',
      comPSearchText: ''
    }
  },
  computed: {
    // 使用计算属性来显示显示类名动态更新的
    computedClassObject () {
      return {
        active: this.isActive && !this.error,
        'text-danger': this.error && this.error.type === 'fatal'
      }
    }
  },
  beforeCreate () {
    console.log('父组件beforeCreate')
  },
  created () {
    // 使用 Lodash 实现防抖
    this.debouncedClick = _.debounce(this.click, 500)
    console.log('父组件created')
  },
  beforeMount () {
    console.log('父组件beforeMount')
  },
  mounted () {
    console.log('父组件mounted')
  },
  beforeUpdate () {
    console.log('父组件beforeUpdate')
  },
  updated () {
    console.log('父组件updated')
  },
  beforeUnmount () {
    console.log('父组件beforeUnmount')
  },
  unmounted () {
    // 移除组件时，取消定时器
    this.debouncedClick.cancel()
    console.log('父组件unmounted')
  },
  methods: {
    click () {
      console.log('123')
    },
    // 通过方法来进行接受子组件的events
    onEnlargeText (events) {
      this.postFontSize += events
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
