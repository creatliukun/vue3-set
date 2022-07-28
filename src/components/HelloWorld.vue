<template>
  <div class="hello">
    <!-- <h1>{{ msg }}</h1> -->
     <div>{{ msg.split('').reverse().join('') }}</div>
     <button @click="debouncedClick">打印123</button>
  </div>
</template>

<script>
// 防抖和节流，为了使组件实例彼此独立，可以在生命周期钩子的 created 里添加防抖函数，移除组件时，取消防抖函数
import _ from 'lodash'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  created () {
    // 使用 Lodash 实现防抖
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted () {
    // 移除组件时，取消定时器
    this.debouncedClick.cancel()
  },
  methods: {
    click () {
      console.log('123')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
