<template>
    <ul class="navbar" :class="{ hideNavBar }">
        <ToolButton icon="content-save" text="SAVE" @click="canvasToImage"></ToolButton>
        <ToolButton icon="monitor" text="CLEAR ALL" @click="resetCanvas"></ToolButton>
        <ToolButton icon="undo" text="UNDO" @click="back"></ToolButton>
        <ToolButton icon="redo" text="REDO" @click="forward"></ToolButton>
        <HideButton hide-type="navbar" @click="hideNavBar = !hideNavBar"></HideButton>
    </ul>
</template>

<script setup lang="ts">
import ToolButton from '@/components/Navbar.Tool-Button.vue'
import HideButton from '@/components/Tool-Bar-Hide-Button.vue'
import { ref, defineEmits } from 'vue'

const emit = defineEmits({
    canvasToImage: () => true,
    resetCanvas: () => true
})

let hideNavBar = ref(false)

const canvasToImage = () => {
    emit('canvasToImage')
}

const resetCanvas = () => {
    emit('resetCanvas')
}

const back = () => {
    window.history.back()
}
const forward = () => {
    window.history.forward()
}

</script>

<style lang="scss" scoped>
.hideNavBar {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.navbar{
  height: 90px;
  position: fixed;
  top: 0;
  width: 100%;
  background-color: white;
  transition: all 0.1s ;
  display: flex;
  justify-content: center;
  align-items: center;
}

.hideNavBar{
  top: -90px;
  & .btn::after{
    content:"▼";
    line-height: 55px;
  }
}
</style>
