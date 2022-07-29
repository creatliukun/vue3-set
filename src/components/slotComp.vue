<template>
  <!-- 匿名插槽和具名插槽 -->
  <!-- <div> -->
  <!-- 具名插槽 -->
  <!-- <header>
      <slot name="header"></slot>
    </header> -->
  <!-- <main>
      <slot>
        <strong>Error!</strong>
        组件中没有数据
      </slot>
    </main> -->
  <footer>
    <slot name="footer"></slot>
  </footer>
  <!-- </div> -->
  <!-- 作用域插槽 -->
  <ul>
    <li v-for="(item, index) in items" :key="index">
      <slot :item="item"> </slot>
    </li>
  </ul>
  <button @click ="submitForm(12,34)">验证submit事件</button>
</template>

<script>
export default {
// 可以通过 emits 选项在组件上定义发出的事件。
// 与 prop 类型验证类似，如果使用对象语法而不是数组语法定义发出的事件，则可以对它进行验证
  emits: {
    // 没有验证
    click: null,

    // 验证 submit 事件
    submit: ({ email, password }) => {
      console.log(email, password)
      if (email && password) {
        return true
      } else {
        console.warn('Invalid submit event payload!')
        return false
      }
    }
  },
  data () {
    return {
      items: ['父组件', '拿到子组件作用域的数据']
    }
  },
  methods: {
    submitForm (email, password) {
      console.log(this.$emit, 'password112')
      this.$emit('submit', { email, password })
    }
  }
}
</script>
