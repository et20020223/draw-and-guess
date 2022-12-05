<template>
    <li class="toolbar__tool">
        <v-icon
            v-for="tool in tools"
            :icon="`mdi-${tool.name}`"
            :class="isToolActive(tool.name)"
            @click="currentTool = tool.name"
            :key="tool.name">
        </v-icon>
    </li>
</template>

<script setup lang="ts">
import { reactive, ref, defineEmits, watch } from 'vue'

const emit = defineEmits({
    setTool: (_tool: string) => true
})
let tools = reactive([
    {
        name: 'brush'
    },
    {
        name: 'eraser-variant'
    },
    {
        name: 'crop-square'
    }
])
let currentTool = ref('brush')

watch(currentTool, () => {
    emit('setTool', currentTool.value)
})

const isToolActive = (tool: string) => {
    return tool === currentTool.value ? 'active' : ''
}

</script>

<style lang="scss" scoped>
.toolbar__tool{
  i{
      font-size: 20px;
      padding: 30px;
      border-radius: 50px;
      &:hover{
        background-color: #d8d8d8;
      }
  }
  .active{
    background-color: #d8d8d8;
  }
}
</style>
