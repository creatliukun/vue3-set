<template>
  <div>
    {{ title }}
    <!-- 子组件通过$emit来触发父组件的函数 -->
    <button @click="$emit('enlargeText',0.05)">子组件增加文字</button>
    <!-- 子组件里面的双向绑定触发父组件 -->
    <input
      v-model="value"
    >
    <!--   子组件普通双向绑定  :value="modelValue"  @input="$emit('update:modelValue', $event.target.value)" -->
    <!--   子组件使用计算属性进行双向绑定   v-model="value" -->
  </div>
</template>

<script>
export default {
//  <!-- 组件上的v-model等价于  :model-value="comPSearchText"   @update:model-value="comPSearchText = $event" -->
  props: ['title', 'modelValue'],
  emits: ['update:modelValue'],
  beforeCreate () {
    console.log('子组件beforeCreate')
  },
  created () {
    console.log('子组件created')
  },
  beforeMount () {
    console.log('子组件beforeMount')
  },
  mounted () {
    console.log('子组件mounted')
  },
  beforeUpdate () {
    console.log('子组件beforeUpdate')
  },
  updated () {
    console.log('子组件updated')
  },
  beforeUnmount () {
    console.log('子组件beforeUnmount')
  },
  unmounted () {
    console.log('子组件unmounted')
  },
  // 使用计算属性实现数据双项绑定
  computed: {
    value: {
      get () {
        return this.modelValue
      },
      set (value) {
        this.$emit('update:modelValue', value)
      }
    }
  }
}
</script>
