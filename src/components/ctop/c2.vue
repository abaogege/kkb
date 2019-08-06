<template>
    <div>
        <button @click="give">C2子组件派发</button>
        <div >{{c2v}}</div>
        <C3></C3>
    </div>    

</template>
<script>
import C3 from './c3.vue';
export default {
    data() {
        return {
            c2v: 'c2v'
        }
    },
    components: {
        C3,
    },
    methods: {
        give() {
             this.$emit('givename',this.c2v)
        }
    },

    created(){
        this.$parent.$on('tobrother',(e)=>{
            console.log('兄弟收到了',e)
        })
    }
}
</script>

