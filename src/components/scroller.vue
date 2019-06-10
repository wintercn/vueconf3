<template>
    <div ref="body" v-on:scroll="scroll" style="overflow-y:scroll;overflow-x:hidden;"><slot></slot></div>
</template>

<script>
export default {
    props: ['value'],
    methods: {
        scroll: function(){
            this.$emit('input', this.$refs["body"].scrollTop);
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
