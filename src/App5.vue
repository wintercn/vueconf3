<template>
  <div id="app">
    <webgl :t="x"/>
    <br/>
    <input type="range" max="100" min="0" v-model="x" />
    <!--<timer v-model="x"/>-->
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
      x: "0",
      p1: {x:30, y:30},
      p2: {x:150, y:150}
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
