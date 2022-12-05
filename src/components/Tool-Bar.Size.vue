<template>
    <li class="toolbar__size">
        <span>SIZE:</span>
        <input type="text"
               v-model="currentSize"
               class="size-controller">
        <span>px</span>
    </li>
</template>

<script setup lang="ts">
import { ref, defineEmits, watch } from 'vue'

const emit = defineEmits({
    setSize: (_size: number) => true
})

let currentSize = ref(1)

watch(currentSize, () => {
    emit('setSize', currentSize.value)
})

</script>
<style lang="scss" scoped>
.toolbar__size{
  font-size: 20px;
  span{
    margin: 0 20px;
  }
  input{
    text-align: center;
    padding: 10px 20px;
    width: 100px;
    font-size: 20px;
    border:none;
    &:focus{
      outline:none;
    }
  }
}
</style>
