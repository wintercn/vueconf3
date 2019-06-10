<template>
  <div id="app">
    <div style="margin:auto;width:100px;height:100px" 
      v-bind:style="{ backgroundColor: `rgb(${r}, ${g}, ${b})` }"/>
    <br/><input type="range" max="255" min="0" v-model="r" />
    <br/><input type="range" max="255" min="0" v-model="g" />
    <br/><input type="range" max="255" min="0" v-model="b" />
  </div>
</template>

<script>
import webgl from './components/webgl-renderer.vue'
import pointer from './components/pointer-input.vue'

export default {
  name: 'app',
  components: {
    webgl, pointer
  },
  data: function () {
    return {
      r: 0,
      g: 0,
      b: 0
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
