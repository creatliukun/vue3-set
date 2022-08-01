<template>
  <!-- 计算属性和watch的最大区别就是watch可以处理异步的函数 -->
  <div class="about">
    <!-- 计算属性进行渲染 -->
    <div id="computed-basics">
      <p>使用计算属性:</p>
      <span>{{ publishedBooksMessage }}</span>
    </div>
    <!-- watch进行监听 -->
    <div id="watch-example">
      <p>
        使用监听属性发送请求:
        <input v-model="question" />
      </p>
      <p>{{ answer }}</p>
    </div>
  </div>
  <!-- v-for关于 -->
  <div>
    <!-- v-for里面使用数组 -->
    <ul id="array-rendering">
      <li v-for="(item, index) in items" :key="index">
        {{ item.message }}
      </li>
    </ul>
    <!-- v-for里面使用对象 -->
    <!-- 在遍历对象时，会按 Object.keys() 的结果遍历，但是不能保证它在不同 JavaScript 引擎下的结果都一致 -->
    <ul id="array-rendering">
      <li v-for="(value, index) in myObject" :key="index">
        {{ value }}
      </li>
    </ul>
  </div>
  <!-- 事件处理 -->
  <div>
  <!-- 访问原始的 DOM 事件 -->
  <button @click="warn('阻止默认事件.', $event)">访问原始的 dom事件</button>
  <!-- 多个事件处理 -->
  <button @click="one($event), two($event)">多个事件处理</button>
  </div>
  <!-- 事件修饰符 -->
  <div @click="fatherClick">
    <!-- 阻止单击事件继续冒泡 -->
    <a @click.stop="doThis">阻止单击事件继续冒泡</a>
    <!-- 提交事件不再重载页面 -->
    <form @submit.prevent="onSubmit">提交事件不再重载页面</form>
    <button @click="onSubmitNormal">普通的事件会冒泡</button>
  </div>
  <!-- 组件使用插槽 -->
  <slotCompVue>
    <!-- 插槽接受的数据,v-slot只能添加到template上 -->
  <!-- <template v-slot:header>
    <h5>具名插槽header</h5>
  </template> -->

  <!-- <template v-slot:default>
    <p>匿名插槽content</p>
  </template> -->

  <template v-slot:footer>
    <h6>具名插槽footer</h6>
  </template>
  <!-- 作用域插槽，父组件可以拿到子组件的值 -->
  <template v-slot:default="slotProps">
    <span @click="clickGetSlotProps(slotProps)">{{ slotProps.item }}</span>
  </template>
  </slotCompVue>
</template>

<script>
import slotCompVue from '@/components/slotComp.vue'
import axios from 'axios'
export default {
  components: {
    slotCompVue
  },
  data () {
    return {
      author: {
        name: 'John Doe',
        books: [1, 2, 3]
      },
      question: '',
      answer: '请输入问题，最后带上`?`',
      items: [{ message: 'v-for' }, { message: '遍历数组' }],
      myObject: {
        title: 'v-for遍历',
        author: '对象'
      }
    }
  },
  watch: {
    // 使用 watch 选项允许我们执行异步操作 (访问一个 API)，并设置一个执行该操作的条件。这些都是计算属性无法做到的
    // 每当 question 发生变化时，该函数将会执行
    question (newQuestion, oldQuestion) {
      console.log(newQuestion, 'newQuestion')
      if (newQuestion.indexOf('?') > -1) {
        this.getAnswer()
      }
    }
  },
  computed: {
    // 计算属性的 getter函数没有副作用，它更易于测试和理解
    // 计算属性默认只有 getter，不过在需要时你也可以提供一个 setter
    publishedBooksMessage () {
      // `this` 指向 vm 实例
      return this.author.books.length > 0 ? '长度大于0' : '长度不大于0'
    }
  },
  mounted () {
  },

  methods: {
    getAnswer () {
      console.log(this.question, 'question')
      this.answer = '请求中...'
      axios
        .get('https://yesno.wtf/api')
        .then((response) => {
          this.answer = response.data.answer
        })
        .catch((error) => {
          this.answer = '暂时不能访问，请稍后再试. ' + error
        })
    },
    warn (message, event) {
      // 现在可以访问到原生事件
      if (event) {
        // 阻止默认事件
        event.preventDefault()
      }
      alert(message)
    },
    one (event) {
    // 第一个事件处理器逻辑...
      console.log(event, 'one')
    },
    two (event) {
    // 第二个事件处理器逻辑...
      console.log(event, 'two')
    },
    fatherClick () {
      console.log('father')
    },
    doThis () {
      console.log('doThis')
    },
    onSubmit () {
      console.log('onSubmit')
    },
    onSubmitNormal () {
      console.log('onSubmitNormal')
    },
    clickGetSlotProps (sonProps) {
      console.log(sonProps, 'sonProps')
    }
  }
}
</script>
