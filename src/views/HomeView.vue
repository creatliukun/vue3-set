<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <p>使用 v-html 渲染原生 <span v-html="rawHtml"></span></p>

    <HelloWorld msg="这条数据是反的" class="father-class" />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'HomeView',
  components: {
    HelloWorld
  },
  data () {
    return {
      rawHtml: '<div style="color: red">这是红色</div>'
    }
  },
  methods: {
    get () {}
  }
}
</script>
