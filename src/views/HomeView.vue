<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <p>使用 v-html 渲染原生 <span v-html="rawHtml"></span></p>
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
    <!-- setup监听counter -->
    <p>
       setup监听counter:
       <input v-model="counter" />
    </p>
    <p>
       setup的计算属性:
      {{twiceTheCounter}}
    </p>
    <HelloWorld msg="这条数据是反的" class="father-class" />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import { provide, reactive, ref, watch, toRefs, onMounted, computed } from 'vue'

export default {
  name: 'HomeView',
  components: {
    HelloWorld
  },
  setup (props) {
    // 为了增加 provide 值和 inject 值之间的响应性，我们可以在 provide
    // 使用 `toRefs` 创建对 `props` 中的 `user` property 的响应式引用
    const { user } = toRefs(props)
    const location11 = ref('地理位置，添加响应式')
    // 值时使用 ref 或 reactive。
    const counter = ref('1')
    // setup中使用computed监听
    const counterTwo = ref(1)
    const twiceTheCounter = computed(() => counterTwo.value * 2)
    const geolocation = reactive({
      longitude: 90,
      latitude: 135
    })
    // 组件内部更改的方法
    const updateLocation = () => {
      console.log(user, 'user')
      location11.value = 'location11值被更改'
    }
    provide('location', location11)
    provide('geolocation', geolocation)
    provide('updateLocation', updateLocation)
    // 在setup中监听counter
    watch(counter, (newValue, oldValue) => {
      console.log('setup监听counter改变的值：' + counter.value)
    })
    // 和外部的mounted类似，这个时候组件渲染完毕，调用次函数
    onMounted(() => {
      updateLocation()
    })
    // 如果想要setup外部的函数，模版，钩子等获取到此数据，那么就需要return出去
    return {
      location11,
      geolocation,
      updateLocation,
      counter,
      twiceTheCounter
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
