<template>
<div>
  <scroller v-model="s" id="app" style="border:solid 1px grey;height:300px;width:500px;margin:auto;">
    <img style="position:relative;z-index:1" v-bind:style="{top: Math.floor(this.s/2) + 'px'}" src="./assets/1.jpg" width="500"/>
    <img style="position:relative;z-index:2" v-bind:style="{top: this.s > 326 ? Math.floor((this.s-326)/2) + 'px' : 0 + 'px'}" src="./assets/2.jpg" width="500"/>
    <img style="position:relative;z-index:3" v-bind:style="{top: this.s > 646 ? Math.floor((this.s-646)/2) + 'px' : 0 + 'px'}" src="./assets/3.jpg" width="500"/>
    <img style="position:relative;z-index:4" src="./assets/4.jpg" width="500"/>
  </scroller>
  {{s}}
</div>
</template>

<script>
import webgl from './components/webgl-renderer.vue'
import pointer from './components/pointer-input.vue'
import timer from './components/timer.vue'
import scroller from './components/scroller.vue'

export default {
  name: 'app',
  components: {
    webgl, pointer, timer, scroller
  },
  data: function () {
    return {
      s: 0
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
