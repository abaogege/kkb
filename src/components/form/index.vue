<template>
<div>
    <QForm :model="model" :rules="rules" ref="QForm">
        <QFormItem :labelname="'姓名'" prop="username">
            <QInput v-model="model.username" :type="'text'"></QInput>
        </QFormItem>
        <QFormItem :labelname="'密码'" prop="pwd">
            <QInput v-model="model.pwd" :type="'password'"></QInput>
        </QFormItem>
        <QFormItem>
            <button @click="doSubmit">提交</button>
        </QFormItem>
    </QForm>
</div>
</template>

<script>
import QInput from "./QInput.vue";
import QFormItem from "./QFormItem.vue";
import QForm from "./QForm.vue";
export default {
    data() {
        return {
            model: {
                username: "",
                pwd: ""
            },
            rules: {
                username: [{
                    required: true,
                    message: "用户名必填",
                    trigger: 'blur'
                }, {
                    min: 3,
                    max: 10,
                    message: "用户名长度为3-10"
                }],
                pwd: [{
                    required: true,
                    message: "密码必填",
                    trigger: 'blur'
                }, {
                    min: 6,
                    message: "密码长度为6"
                }]
            }
        };
    },
    components: {
        QInput,
        QFormItem,
        QForm
    },
    methods: {
        doSubmit() {
            this.$refs.QForm.validateForm(function (isPass) {
                if (isPass) {
                    alert('success')
                } else {
                    alert('false')

                }
            })
        }
    },
};
</script>
