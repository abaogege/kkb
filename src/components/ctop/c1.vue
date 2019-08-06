<template>
    <div>
        <button @click="give">C1子组件派发</button>
        <button @click="give2">C1子组件派发给兄弟</button>
        <button @click="toc2">bus传递事件</button>
        <div >{{c1v}}</div>
    </div>    

</template>
<script>
export default {
    data() {
        return {
            c1v: 'c1v'
        }
    },
    methods: {
        give() {
             this.$emit('givename',this.c1v)
             console.log(this.$root)
        },
        give2(){
            this.$parent.$emit('tobrother',this.c1v)
        },
        toc2(){
            this.$bus.$emit('busevent',this.c1v)
        }
    },
}
</script>

