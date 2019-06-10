<template>
    <div style="position:absolute"></div>
</template>

<script>
export default {
    props: ['value'],
    mounted: function(){
        var begin = Date.now();
        var tick = () => {
            this.$emit('input', Date.now() - begin);
            requestAnimationFrame(tick);
        }
        tick();
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
