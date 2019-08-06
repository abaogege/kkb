<template>
<div>
    <p>slot3上</p>
    <p>
        <slot></slot>
    </p>
    <p>
        <slot name="content"></slot>
    </p>
    <p>
        <slot name="scope" :v="value"></slot>

    </p>
    <p>slot3下</p>
</div>
</template>

<script>
export default {
    data() {
        return {
            value: 'slot3content'
        }
    },
}
</script>
