<template>
  <div id="app">
    <el-button @click="click1">显示</el-button>
    <el-button @click="click2">隐藏</el-button>
    <el-button @click="click3">更新</el-button>
    <Subscription v-if="show" @changeSchema="changeSchema"/>
  </div>
</template>

<script>
import Subscription from './components/Subscription';
import schema from './schema/1' 
import schema2 from './schema/2'
import { setTimeout } from 'timers';

export default {
  name: 'app',
  data () {
    return {
      show: false
    }
  },
  components: {
    Subscription,
  },
  mounted () {
    console.log(schema)
    console.log(schema2)
    // this.$on('changeSchema', () => {
    //   console.log('1')
    // })
    this.click1();
  },
  methods: {
    changeSchema () {
      console.log('1')
      this.show = false
      this.click3()
    },
    click1 () {
      console.log('c1')
      let data = JSON.stringify(schema)
      sessionStorage.setItem('schema', data)
      this.show = true
    },
    click2 () {
      console.log('c2')
      sessionStorage.setItem('schema', '')
      this.show = false
    },
    click3 () {
      console.log('c3')
      this.show = false
      let data = JSON.stringify(schema2)
      sessionStorage.setItem('schema', data)
      setTimeout(() => {
        this.show = true
      }, 1000)
    }
  }
};
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
