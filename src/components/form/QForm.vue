<template>
<div>
    <slot></slot>
</div>
</template>

<script>
import {
    Promise
} from 'q';
export default {

    props: {
        model: {
            type: Object,
            required: true,
        },
        rules: {
            type: Object,
        }
    },

    provide() {
        return {
            form: this
        }
    },
    data() {
        return {
            
        }
    },
    mounted() {
    },
    methods: {
        validateForm(fn) {
            const tasks = this.$children.filter(v => v.prop).map(v => v.validate());
            Promise.all(tasks).then(() => fn(true)).catch(() => fn(false))
        }
    },
}
</script>
