<template>
    <v-row class="toolbar" :class="{ hideToolBar }">
        <HideButton hide-type="toolbar" @click="hideToolBar = !hideToolBar"></HideButton>
        <DrawTool @set-tool="setTool"></DrawTool>
        <SizeTool @set-size="setSize"></SizeTool>
        <ColorTool @set-color="setColor"></ColorTool>
    </v-row>
</template>

<script setup lang="ts">
import HideButton from '@/components/Tool-Bar-Hide-Button.vue'
import DrawTool from '@/components/Tool-Bar.Tool.vue'
import ColorTool from '@/components/Tool-Bar.Color.vue'
import SizeTool from '@/components/Tool-Bar.Size.vue'
import { ref, defineEmits } from 'vue'

const emit = defineEmits({
    setColor: (_color: string) => true,
    setTool: (_tool: string) => true,
    setSize: (_size: number) => true
})

let hideToolBar = ref(false)

const setTool = (tool: string) => {
    emit('setTool', tool)
}

const setSize = (size: number) => {
    emit('setSize', size)
}

const setColor = (color: string) => {
    emit('setColor', color)
}

</script>

<style lang="scss" scoped>
.toolbar{
  margin: 0 auto;
  position: fixed;
  bottom: 100px;
  left: 0;
  right: 0;
  transform: translateY(0);
  width: 70%;
  height: 100px;
  background-color: #fff;
  border-radius: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  li{
    list-style: none;
  }
}
</style>
