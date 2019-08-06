<template>
    <div>
        <Slot3>
            从slot2插入的内容  
        </Slot3>
        <Slot3>
            <template v-slot:default>
                从slot2插入匿名插槽的内容
            </template>
            <template v-slot:content>
                从slot2插入具名插槽的内容
            </template>
            <template v-slot:scope="slot2p">
                {{slot2p.v}}
            </template>
        </Slot3>
    </div>
</template>
<script>
import Slot3 from './slot3.vue';
export default {
    components: {
        Slot3,
    },
    data() {
        return {
            value: 'slot2value'
        }
    },
}
</script>
