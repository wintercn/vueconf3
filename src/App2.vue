<template>
  <div id="app">
    <div style="margin:auto;width:100px;height:100px" v-bind:style="{ backgroundColor: `hsl(${Math.floor(t/10)%360}, 100%, 50%)` }"/>
    <timer v-model="t" />

  </div>
</template>

<script>
import webgl from './components/webgl-renderer.vue'
import pointer from './components/pointer-input.vue'
import timer from './components/timer.vue'

export default {
  name: 'app',
  components: {
    webgl, pointer, timer
  },
  data: function () {
    return {
      t: 0
    }
  }
}
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
