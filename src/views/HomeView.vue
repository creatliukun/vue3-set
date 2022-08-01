<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <p>使用 v-html 渲染原生 <span v-html="rawHtml"></span></p>

    <HelloWorld msg="这条数据是反的" class="father-class" />
    <button @click="changeArrLength">改变数组长度</button>
    <button @click="updateLocation11">改变位置</button>
    <div>{{todos.length}}</div>
    <p>
        监听位置:
       <input v-model="watchGeolocation" />
      </p>
    <p>
        location11:
       <input v-model="location11" />
      </p>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import { provide, reactive, ref } from 'vue'

export default {
  name: 'HomeView',
  components: {
    HelloWorld
  },
  setup () {
    // 为了增加 provide 值和 inject 值之间的响应性，我们可以在 provide
    // 值时使用 ref 或 reactive。
    const location11 = ref('地理位置，添加响应式')
    const geolocation = reactive({
      longitude: 90,
      latitude: 135
    })
    // 组件内部更改的方法
    const updateLocation = () => {
      location11.value = 'location11值被更改'
    }
    provide('location', location11)
    provide('geolocation', geolocation)
    provide('updateLocation', updateLocation)
    return {
      location11,
      geolocation,
      updateLocation
    }
    // provide('location', '地理位置')
    // provide('geolocation', {
    //   longitude: 90,
    //   latitude: 135
    // })
  },
  // 多层嵌套使用provide inject
  // provide: {
  //   user: 'provide-inject'
  // },
  // 如果要访问组件实例 property，我们需要将 provide 转换为返回对象的函数：
  provide () {
    console.log('组父组件provide')
    return {
      user: 'provide-inject',
      // 组件实例 property
      // todoLength: this.todos.length
      // 使用计算属性也可以写在provide里面
      todoLength: this.dataToAction,
      // 这里的优先级大于setup中的优先级
      location: 'North Pole',
      geolocation: {
        longitude: 90,
        latitude: 135
      }
    }
  },
  computed: {
    dataToAction () {
      console.log('组父组件computed')
      return this.todos.length
    }
  },
  watch: {
    watchGeolocation (newVal, oldVal) {
      console.log('组父组件watch,', newVal, oldVal)
      return this.todos.length
    }
  },
  created () {
    console.log('组父组件created')
  },
  beforeMount () {
    console.log('组父组件beforeMount')
  },
  mounted () {
    console.log('组父组件mounted')
  },
  beforeUpdate () {
    console.log('组父组件beforeUpdate')
  },
  updated () {
    console.log('组父组件updated')
  },
  beforeUnmount () {
    console.log('组父组件beforeUnmount')
  },
  unmounted () {
    // 移除组件时，取消定时器
    this.debouncedClick.cancel()
    console.log('组父组件unmounted')
  },

  data () {
    return {
      rawHtml: '<div style="color: red">这是红色</div>',
      todos: ['Feed a cat', 'Buy tickets'],
      watchGeolocation: ''
    }
  },
  methods: {
    get () {},
    changeArrLength () {
      this.todos.push('加一个长度')
    },
    updateLocation11 () {
      console.log('改变location')
      this.location11 = '改变后的location'
    }
  }
}
</script>
