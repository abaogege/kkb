<template>
<div>
    <label>{{labelname}}</label>
    <slot></slot>
    <p>{{errormsg}}</p>
</div>
</template>

<script>
import AValidator from 'async-validator'

export default {
    inject: ["form"],
    data() {
        return {
            errormsg: ''
        }
    },
    props: {
        labelname: String,
        prop: String,
    },
    created() {
        
    },
    mounted() {
        this.$on("validate", () => {
            this.validate();
        });
    },
    updated(){},
    methods: {
        validate() {
            const rules = this.form.rules[this.prop];
            const values = this.form.model[this.prop];
            const desc = {
                [this.prop]: rules
            };
            const descValue = {
                [this.prop]: values
            }
            const validator = new AValidator(desc)
            return validator.validate(descValue, (errors, fields) => {
                if (!errors) {
                    this.errormsg = ''
                } else {
                    this.errormsg = errors[0].message
                }
            })
        }
    },
}
</script>
