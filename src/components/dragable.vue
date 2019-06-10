<template>
    <div style="position:absolute;overflow:visible;" 
        v-bind:style="{ top: value.y + 'px', left: value.x + 'px' }"
        v-on:mousedown="start"
        onselectstart="event.preventDefault();return false;"
        ><slot></slot></div>
</template>

<script>
export default {
    props: ['value'],
    methods: {
        start: function(event){
            this.startX = event.clientX;
            this.startY = event.clientY;
            this.startValue = {...this.value};
            this.dragging = false;
            var move = event => {
                if(Math.abs(event.clientX - this.startX) > 3 || 
                    Math.abs(event.clientY - this.startY) > 3 ) 
                    this.dragging = true;
                if(this.dragging) {
                    this.$emit('input', {
                        x: this.startValue.x + event.clientX - this.startX,
                        y: this.startValue.y + event.clientY - this.startY
                    })
                }
            }

            var end = event => {
                document.removeEventListener('mousemove', move, false);
                document.removeEventListener('mouseup', end, false);
                this.dragging = false;
            }
            document.addEventListener('mousemove', move, false);
            document.addEventListener('mouseup', end, false);
        }
    }
    

    
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
