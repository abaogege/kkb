<template>
    <div>
        <div>{{v}}</div>
        <div>{{obj.name}}</div>
    </div>    

</template>
<script>
export default {
    data() {
        return {
            c3v: 'c3v'
        }
    },
   inject:{
       v:'value',
       obj:'o',
   },
   created(){
       this.$bus.$on('busevent',(e)=>{
           console.log('我通过bus总线拿到了'+ e)
       })
   }
}
</script>

