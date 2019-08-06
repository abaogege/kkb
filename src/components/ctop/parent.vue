<template>
    <div>
        <C1 @givename = "listen">
        </C1>
        <C2>

        </C2>
    </div>
</template>

<script>
import C1 from './c1.vue';
import C2 from './c2.vue';
export default {
    components: {
        C1,
        C2
    },
    provide(){
        return{
            value:123,
            o:{name:'abao',age:18}
        }
    },
    methods: {
        listen(e) {
            console.log('listen',e)
        }
    },
    
}
</script>
