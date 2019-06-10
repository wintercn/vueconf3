<template>
    <div ref="body" style="z-index:100;background-color:lightblue;width:10px;height:10px;position:absolute;transform:translate(-50%, -50%)" 
        v-bind:style="{ top: noY ? this.y + 'px' : this.value.y + 'px', left: noX ? this.x + 'px':  this.value.x + 'px' }"
        v-on:mousedown="start"
        onselectstart="event.preventDefault();return false;"
        ></div>
</template>

<script>
export default {
    props: ['value', 'noX', 'noY', 'x', 'y'],
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
                    });
                    //console.log(this.$props.noY ? this.$props.y : this.$props.value.y + "px");
                    //this.$refs["body"].style.top = this.$props.noY ? this.$props.y : this.$props.value.y + "px";
                    //this.$refs["body"].style.left = this.$props.noX ? this.$props.x : this.$props.value.x + "px";
                }
                event.stopPropagation();
                event.stopImmediatePropagation();
                event.cancelBubble = true;
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
