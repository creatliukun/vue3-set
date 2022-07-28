<template>
  <div class="about">
    <div id="computed-basics">
      <p>使用计算属性:</p>
      <span>{{ publishedBooksMessage }}</span>
    </div>
    <div id="watch-example">
      <p>
        使用监听属性发送请求:
        <input v-model="question" />
      </p>
      <p>{{ answer }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      author: {
        name: 'John Doe',
        books: [1, 2, 3]
      },
      question: '',
      answer: '请输入问题，最后带上`?`'
    }
  },
  watch: {
    // 使用 watch 选项允许我们执行异步操作 (访问一个 API)，并设置一个执行该操作的条件。这些都是计算属性无法做到的
    // 每当 question 发生变化时，该函数将会执行
    question (newQuestion, oldQuestion) {
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

  methods: {
    getAnswer () {
      this.answer = '请求中...'
      axios
        .get('https://yesno.wtf/api')
        .then(response => {
          this.answer = response.data.answer
        })
        .catch(error => {
          this.answer = '暂时不能访问，请稍后再试. ' + error
        })
    }
  }
}
</script>
