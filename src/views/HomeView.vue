<template>
  <div class="home" v-if=false>
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
  <div v-else>
   <div v-for="(item, i) in list" :ref="el => { if (el) divs[i] = el }" :key="i">
    {{ item }}
  </div>
  <div ref="rootr">这是rootr元素</div>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import { provide, reactive, ref, watch, toRefs, onMounted, computed, onBeforeUpdate, watchEffect, onBeforeMount } from 'vue'

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
    // 在setup中使用v-for
    const list = reactive(['reactive', '赋值', 'setup使用v-for'])
    const divs = ref([])
    // 测试watchEffect
    const rootr = ref(null)
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
    watch(rootr, (newValue, oldValue) => {
      console.log('setup监听rootr改变的值：', rootr.value, newValue, oldValue)
    })
    // watchEffect会执行打印，但是watch并没有执行.看打印watchEffect执行了两次,其实是因为watch进行了监听rootr改变了，也引起了副作用的执行
    watchEffect(() => {
      // 这个副作用在 DOM 更新之前运行，因此，模板引用还没有持有对元素的引用。
      console.log(rootr.value) // => null
    },
    // 使用模板引用的侦听器应该用 flush: 'post' 选项来定义，这将在 DOM 更新后运行副作用，确保模板引用与 DOM 保持同步，并引用正确的元素
    {
      flush: 'post'
    })

    // 确保在每次更新之前重置ref
    onBeforeUpdate(() => {
      console.log('setup onBeforeUpdate')
      divs.value = []
    })
    // 和外部的mounted类似，这个时候组件渲染完毕，调用次函数
    onMounted(() => {
      console.log('setup onMounted')
      console.log(divs, 'divs')
      // updateLocation()
    })
    onBeforeMount(() => {
      console.log('setup onBeforeMount')
    })
    // 如果想要setup外部的函数，模版，钩子等获取到此数据，那么就需要return出去
    return {
      location11,
      geolocation,
      updateLocation,
      counter,
      twiceTheCounter,
      list,
      divs,
      rootr
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
