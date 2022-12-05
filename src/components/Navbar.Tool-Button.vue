<template>
    <li @click="click">
        <v-icon :icon="`mdi-${icon}`" />
        <div>{{ text }}</div>
    </li>
</template>

<script setup lang="ts">
import { defineProps, defineEmits } from 'vue'

defineProps({
    icon: {
        type: String,
        required: true
    },
    text: {
        type: String,
        required: true
    }
})

const emit = defineEmits({
    click: () => true
})

const click = () => {
    emit('click')
}

</script>

<style lang="scss" scoped>
li{
    font-size: 20px;
    margin:0 40px;
    padding: 0 20px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    list-style: none;
    &:hover{
    background-color: #efefef;
     cursor: pointer;
    }
}

</style>
