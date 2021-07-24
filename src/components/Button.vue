<template>
    <button @click="$emit('btn-click')" :style="{background: color}" class="btn">{{text}}</button>    
</template>

<script>
export default {
    name: 'Button',
    props: {
        text: String,
        color: String,
    },
    emit: ['btn-click']
    
}
</script>

<style>
    
</style>