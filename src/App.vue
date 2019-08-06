<template>
<div id="app">
    <FormIndex></FormIndex>
    <Parent></Parent>
    <hr>
    <Slot2></Slot2>
</div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue';
import FormIndex from './components/form';
import Parent from './components/ctop/parent.vue';

import Slot2 from './components/slot/slot2.vue';


export default {
    name: 'app',
    components: {
        FormIndex,
        Parent,
        Slot2
    },
    data() {
       return {}
    },
    computed: {

    },
    created() {
       
    },
    mounted() {
       
    },
    updated() {
        
    },
    methods: {

    },
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
