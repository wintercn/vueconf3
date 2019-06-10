<template>
    <div id="app"> 
        <dragable v-model="base" style="position:absolute;width:0;height:0;overflow:visible;">
            <svg style="position:relative;width:auto;height:auto;overflow:visible;">
                <polygon v-bind:points="`${p1.x},${p1.y} ${p2.x},${p1.y} ${p2.x},${p3.y} ${p4.x},${p4.y} ${p2.x},${p4.y * 2 - p3.y} ${p2.x},${p4.y * 2 - p1.y}  ${p1.x},${p4.y * 2 - p1.y} ${p1.x},${p1.y}`"
                    style="fill:#cccccc;
                    stroke:#000000;stroke-width:1"/>
            </svg>
            <pointer v-model="p1" v-on:input="p2.y = $event.y"></pointer>
            <pointer v-model="p2" v-on:input="p1.y = $event.y;p3.x = $event.x"></pointer>
            <pointer v-model="p3" v-on:input="p2.x = $event.x"></pointer>
            <pointer v-model="p4"></pointer>
        </dragable>
    </div>
</template>

<script>
import webgl from './components/webgl-renderer.vue'
import pointer from './components/pointer-input.vue'
import timer from './components/timer.vue'
import scroller from './components/scroller.vue'
import dragable from './components/dragable.vue'

export default {
  name: 'app',
  components: {
    webgl, pointer, timer, scroller, dragable
  },
  data: function () {
    return {
        base: {x:0, y:0},
        p1: {x:150, y:150},
        p2: {x:250, y:150},
        p3: {x:250, y:100},
        p4: {x:350, y:200}
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
