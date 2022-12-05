<template>
    <div class="btn" :class="buttonClass" @click="trigger"></div>
</template>

<script setup lang="ts">
import { computed, defineEmits, type PropType } from 'vue'

const props = defineProps({
    hideType: {
        type: String as PropType<'navbar' | 'toolbar'>,
        required: true
    }
})

const emit = defineEmits({
    click: () => true
})

const buttonClass = computed(() => {
    return props.hideType === 'navbar' ? 'up' : 'btn-toolbar down'
})

const trigger = () => {
    emit('click')
}

</script>

<style lang="scss" scoped>

.btn{
    position: absolute;
    text-align: center;
    bottom: 0%;
    &:after{
      display: inline-block;
      height: 40px;
      width: 80px;
      background-color: white;
      border-radius: 10px;
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      cursor: pointer;
    }
    &.down::after{
      content:"▼";
      line-height: 40px;
    }
    &.up::after{
      content:"▲";
      line-height: 50px;
    }
}
.btn-toolbar{
  position: fixed;
  left: 50%;
  bottom:100px;
  transform: translateX(-50%);
}

</style>
