#   实现form组件
##  目标

实现简单的form组件，可以进行数据的输入，验证，以及错误的展示

##  结构

    |-  index.vue       展示页
    |-  QForm.vue       组件外框
    |-  QFormItem.vue   组件项，包括label,错误提示信息
    |-  QFormInput.vue  表单输入框，包括双向数据绑定

##  步骤

1.  `QFormInput` 的双向绑定

vue里的input都是具有数据双向绑定结构的特性，但是自定义组件却不能直接使用`v-model`，`v-model` 实际上是一个语法糖，是对 `:value` 和 `@input` 的封装

*index*
```html

<template>
    <div>
        <QInput v-model="userData"> </QInput>
        {{userData}}
    </div>
</template>

```

```js

import QInput from './QInput.vue';

export default {
    data(){
        return{
            userData:'abao'
        }
    },
    components: {
        QInput,
    },
}
```

*QInput*
```html
<template>
    <input :value="value"  @input="onInput">
</template>
```
```js
export default {
   props: {
       value: {
           type: String,
           default: ''
       },
   },
   methods: {
       onInput(e) {
           this.$emit('input',e.target.value)
       }
   },
}
```
子组件每次输入的时候触发onInput事件，派发input事件到父组件，父组件的`v-model`写法其实相当于
```html
    <QInput :value = "userData" @input="userData = $event.target.value"> </QInput>
```


2.  输入框的动态类型

输入框是有类型的，但是直接在`QInput`组件里写死就不方便，因此需要由父组件传递。此时可以用声明`props`接受`type`,也可以使用`$attrs`

>   包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件——在创建高级别的组件时非常有用。

*QInput*

```html
    <input :value="value"  @input="onInput" v-bind="$attrs">
```
*index*
```html
<QInput v-model="userData" type="text"> </QInput>
<QInput v-model="password" type="password"> </QInput>
```

可以看到dom结构都加上了对应的类型，但是`QInput`组件的外层标签也加上了`type`，这时候需要避免顶层容器继承属性。

*QInput*
```js
    inheritAttrs: false,
```

外层容器就没有继承属性了

3.  QFormItem结构
   
`QFormItem`组件作为`QInput`的父组件，需要展示labelname,errmsg,实现验证功能
结构设计如下

*QFormItem*
```html
<template>
    <div>
        <label>{{labelname}}</label>
        <slot></slot>
        <p>{{errormsg}}</p>
    </div>
</template>
```

```js
export default {
    data(){
        return{
            errormsg:''
        }
    },
    props: {
        labelname: {
            type: String,
            required:true 
        },
    },
}

```

因为`labelname`应该是用户决定的，放在`props`里传入，`errormsg`是该组件自身验证后产生的，放在`data`里返回。

*index*
```html
<QFormItem :labelname="'姓名'">
    <QInput v-model="userData" :type="'text'"> </QInput>
</QFormItem>
<QFormItem :labelname="'密码'">
    <QInput v-model="password" :type="'password'"> </QInput>
</QFormItem>
```

页面就出现了需要的效果了


4.  QForm结构

`QForm`就是一个包裹的外层，只需要简单的结构支持，需要接受一个对象，用于收集录入信息；需要接受一个对象，用于收集校验规则

*QForm*
```html
<template>
    <div>
        <slot></slot>
    </div>
</template>

```

```js
    props: {
        model: {
            type: Object,
            required: true,
        },
        rules: {
            type: Object,
        }
    },
```

在index里暂时传入写死的`model`和`rules`

*index*
```html
<template>
<div>
    <QForm :model="model" :rules="rules">
        <QFormItem :labelname="'姓名'">
            <QInput v-model="model.username" :type="'text'"> </QInput>
        </QFormItem>
        <QFormItem :labelname="'密码'">
            <QInput v-model="model.pwd" :type="'password'"> </QInput>
        </QFormItem>
    </QForm>
</div>
</template>


```
```js

    data() {
        return {
            model: {
                username: "abao",
                pwd: "12345"
            },
            rules: {}
        };
    },


```

5.  组件通信

考虑到进行数据验证时候需要进行数据通信，且有可能不仅仅是父子组件之间的通信，在公共组件库/插件设计的时候，往往使用`provide`和`inject`方法进行通信。

>   provide 和 inject 主要为高阶插件/组件库提供用例。并不推荐直接用于应用程序代码中。

>   这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。

>   provide 选项应该是一个对象或返回一个对象的函数。该对象包含可注入其子孙的属性。在该对象中你可以使用 ES2015 Symbols 作为 key，但是只在原生支持 Symbol 和 Reflect.ownKeys 的环境下可工作。

>   inject 选项应该是一个字符串数组，或一个对象，对象的 key 是本地的绑定名，value 是在可用的注入内容中搜索用的 key (字符串或 Symbol)，或一个对象，该对象的from 属性是在可用的注入内容中搜索用的 key (字符串或 Symbol),default 属性是降级情况下使用的 value


通俗的说，在组件的`provide`选项里的配置，在它的子/孙组件`inject`后，子/孙组件可以访问爷组件的值

*QForm*
```js
    provide(){
        return{
            form : this
        }
    },
    data(){
        return{
            a:1
        }
    }
```
*QFormItem*

```js
    inject:["form"],
    created(){
        console.log(this.form.a) //1
    }
```


6.async-validator 验证插件

`QFormItem`组件获得了父组件的实例引用后，可以开始做验证相关的逻辑了。
首先安装`async-validator`插件

```
yarn add async-validator --save
```

安装好之后在`QFormItem`组件引入

*QFormItem*
```js
import AValidator from 'async-validator'
```

用法参考[github](https://github.com/tmpfs/async-validate#usage)

在QFormItem里面添加validate函数，在validate函数内，先声明校验规则，但是不同类型的输入框，规则是不一样，比如用户名可以是中文，但密码不可以，年龄只能为数值等等。因此在传递rules时，需要多一个属性区分，这里我们传入prop作为键值识别。


*index*
```html
<QForm :model="model" :rules="rules">
    <QFormItem :labelname="'姓名'" prop="username">
        <QInput v-model="model.username" :type="'text'"></QInput>
    </QFormItem>
    <QFormItem :labelname="'密码'" prop="pwd">
        <QInput v-model="model.pwd" :type="'password'"></QInput>
    </QFormItem>
</QForm>
```

```js
//定义rules
    data() {
        return {
            ...
            rules: {
                username: [{
                    required: true,
                    message: "用户名必填"
                }, {
                    min: 3,
                    max: 10,
                    message: "用户名长度为3-10"
                }],
                pwd: [{
                    required: true,
                    message: "密码必填"
                }, {
                    min: 6,
                    message: "用户名长度为6"
                }]
            }
        };
    },
```

*QFormItem*
```js
    props:{
        ...
        prop:String
    }
    methods: {
        validate() {
            const rules = this.form.rules[prop] //通过inject注入的form实例取到rules值；
        }
    },
```

同样的，声明需要校验的数值。然后将它们包装一下，符合validate的参数要求


*QFormItem*
```js
    methods: {
        validate() {
            const rules = this.form.rules[prop] //通过inject注入的form实例取到rules值；
            const values = this.form.model[prop]
            const desc = {
                [this.prop]: rules;
            }; //->  {usename:this.form.rules[username] }
            const descValue = {
                [this.prop]: values
            } //->  {usename:this.form.model[username] }

        }
    },
```

实例化async-validate并执行validate方法


*QFormItem*
```js
    methods: {
        validate() {
            ...
            const validator = new AValidator(desc)
            validator.validate(descValue, (errors, fields) => {
                if (!errors) {//errors 为null 说明校验通过
                    this.errormsg = ''
                } else {//errors 不为null 说明校验不通过
                    this.errormsg = errors[0].message
                }
            })
        }
    },
```


好了，此时在钩子函数里触发一下

*QFormItem*
```js
    created() {
         this.validate();
    },
```

可以看到没通过校验的密码错误信息显示出来了

7.  绑定输入验证

我们设想的是在输入信息的过程中，每一次的输入都会触发验证，这时候就要在QInput组件去触发事件

*QInput*
```js
    methods: {
        onInput(e) {
            this.$emit('input', e.target.value);
            this.$parent.$emit('validate'); //每次输入触发$parent的validate事件
        }
    },
```
同时在QFormItem里监听事件

*QFormItem*
```js
    mounted() {
        this.$on("validate", () => {
            this.validate();
        });
    },
```

8.  表单全局验证

单个input的验证实现后，我们要考虑实现全局的验证，即对整个form表单的验证

在index组件上添加提交按钮，并添加点击事件；由于只是一个按钮，不需要prop来与model和rules进行绑定，不需要传递prop；
为了操作QForm组件，我们为QForm添加ref值


*index*
```html

<QForm :model="model" :rules="rules" ref="QForm">
    ...
    <QFormItem>
        <button @click="doSubmit">提交</button>
    </QFormItem>
</QForm>

```

```js
     methods: {
        doSubmit() {
            
        }
    },
```
然后因为全局验证是在表单层级上进行的，我们在QForm组件上添加方法。在方法里我们需要收集各个子组件的验证状态，全部验证通过时，则全局验证通过。async-validate返回的是一个Promise对象，为了获取到子组件的验证状态，我们先将其返回

*QFormItem*
```js

    methods: {
        validate() {
            ...
            const validator = new AValidator(desc);
            //将其return出去
            return validator.validate(descValue, (errors, fields) => {
                if (!errors) {//errors 为null 说明校验通过
                    this.errormsg = ''
                } else {//errors 不为null 说明校验不通过
                    this.errormsg = errors[0].message
                }
            })
        }
    },

```
在QForm里收集

*QForm*
```js
    methods: {
        validateForm() {
            const tasks = this.$children.
                    filter(v => v.prop).//过滤掉不传prop属性的子组件
                    map(v => v.validate());//执行子组件的validate方法获得返回值
        }
    },

```

然后检验tasks,并传入回调函数，根据不同的检验结果传入不同的参数


*QForm*
```js
    methods: {
        validateForm(cb) {
            const tasks = this.$children.
                    filter(v => v.prop).//过滤掉不传prop属性的子组件
                    map(v => v.validate());//执行子组件的validate方法获得返回值
            Promise.all(tasks).then(() => cb(true)).catch(() => cb(false))
        }
    },

```
我们在index里调用QForm的validateForm方法

*index*
```js
    methods: {
        doSubmit() {
            this.$refs.QForm.validateForm(function(isPass){
                if(isPass){
                    alert('success')
                }else{
                    alert('false')

                }
            })
        }
    },
```



9.  样式添加


10. 优化
