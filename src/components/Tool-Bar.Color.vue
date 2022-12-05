<template>
    <li class="toolbar__color">
        <span>COLOR:</span>
        <v-menu v-model="menu" top nudge-bottom="105" nudge-left="16" :close-on-content-click="false">
            <template v-slot:activator="{ props }">
                <div class="color-pick" :style="`background-color: ${currentColor}`" v-bind="props"></div>
            </template>
            <v-card>
                <v-card-text class="pa-0">
                    <v-color-picker v-model="currentColor" hide-inputs flat />
                </v-card-text>
            </v-card>
        </v-menu>
    </li>
</template>

<script setup lang="ts">
import { ref, defineEmits, watch } from 'vue'

const emit = defineEmits({
    setColor: (_color: string) => true
})

let currentColor = ref('#000000')
let menu = ref(false)

watch(currentColor, () => {
    emit('setColor', currentColor.value)
})

</script>

<style lang="scss" scoped>

.toolbar__color{
  display: flex;
  align-items: center;
  span{
    font-size: 20px;
    margin:0 10px;
  }
  .color-pick {
      display: inline-block;
      cursor: pointer;
      height: 30px;
      width: 50px;
      border-radius: 4px;
      margin-left: 10px;
      transition: border-radius 200ms ease-in-out;
  }
}
</style>
