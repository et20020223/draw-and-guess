<template>
    <div class="draw-pad">
        <Navbar @canvasToImage="canvasToImage" @resetCanvas="resetCanvas"></Navbar>
        <canvas
            ref="sketchpad"
            @mousedown="onCanvasMouseDown"
            @mouseup="onCanvasMouseUp"
            :style="`background-color:${backgroundColor}; `">
        </canvas>
        <ToolBar @set-color="setColor" @set-size="setSize" @set-tool="setTool"></ToolBar>
    </div>
</template>

<script setup lang="ts">
import Navbar from '@/components/Navbar.vue'
import ToolBar from '@/components/Tool-Bar.vue'
import { onMounted, ref, nextTick, onUnmounted } from 'vue'
type Action = (_canvas: CanvasRenderingContext2D) => void;
type Coordinate = { x: number, y: number }
// sample: https://www.muji.dev/2019/03/03/canvas-sketchpad/
// https://kernhanda.github.io/tutorial-typescript-canvas-drawing/
const sketchpad = ref<HTMLCanvasElement>()
let canvasContext: CanvasRenderingContext2D
let backgroundColor = ref('#dfdfdf')
let currentColor = ref('#000000')
let currentSize = ref(1)
let isCanvasMouseDown = false
let currentTool = ref('brush')
let tempPosition = {} as Coordinate
let initPosition = {} as Coordinate
let tempCanvas: ImageData

onMounted(() => {
    setCanvas()
    addEventListenerMousemove()
    nextTick(() => {
        window.addEventListener('resize', canvasResize, false)
    })
})

onUnmounted(() => {
    window.removeEventListener('resize', canvasResize)
})

const canvasResize = () => {
    let inMemCanvas = copyDrawImage()
    const canvas = sketchpad.value!
    canvas.width = window.innerWidth
    canvas.height = window.innerHeight - 60
    canvasContext.drawImage(inMemCanvas, 0, 0)
}

const copyDrawImage = () => {
    let inMemCanvas = document.createElement('canvas')
    let inMemCtx = inMemCanvas.getContext('2d')
    const canvas = sketchpad.value!
    inMemCanvas.width = canvas.width
    inMemCanvas.height = canvas.height
    inMemCtx?.drawImage(sketchpad.value!, 0, 0)
    return inMemCanvas
}

const setColor = (color: string) => {
    currentColor.value = color
}

const setSize = (size: number) => {
    currentSize.value = size
}

const setTool = (tool: string) => {
    currentTool.value = tool
}

const onCanvasMouseDown = () => {
    isCanvasMouseDown = true
    setTempCanvas()
}

const onCanvasMouseUp = () => {
    saveCanvasToHistory()
    resetToolState()
}

const canvasToImage = () => {
    const url = sketchpad.value!.toDataURL('image/png', 1.0)
    const link = document.createElement('a')
    link.innerText = 'Download'
    link.href = url
    link.download = 'draw.png'
    link.click()
}

const addEventListenerMousemove = () => {
    window.addEventListener('mousemove', (event) => {
        const currentPosition = getCanvasMousePosition(event.clientX, event.clientY)
        if (isCanvasMouseDown && tempPosition) useToolDraw(event, currentPosition)
        if (currentTool.value === 'square' && isCanvasMouseDown) return
        setCanvasTempPosition(currentPosition.x, currentPosition.y)
    })
    addEventListenerPopstate()
}

const useToolDraw = (event: MouseEvent, currentPos: Coordinate) => {
    const position = getCanvasMousePosition(event.clientX, event.clientY)
    switch (currentTool.value) {
    case 'brush' :
        draw((ctx: CanvasRenderingContext2D) => {
            ctx.strokeStyle = currentColor.value
            ctx.moveTo(tempPosition.x, tempPosition.y)
            ctx.lineTo(position.x, position.y)
        })
        break
    case 'eraser-variant' :
        draw((ctx: CanvasRenderingContext2D) => {
            ctx.strokeStyle = backgroundColor.value
            ctx.arc(tempPosition.x, tempPosition.y, currentSize.value, 0, Math.PI * 2, false)
        })
        break
    case 'crop-square' :
        addEventListenerMousedownPosition()
        draw((ctx: CanvasRenderingContext2D) => {
            ctx.strokeStyle = currentColor.value
            let width = currentPos.x - initPosition.x
            let height = currentPos.y - initPosition.y
            ctx.putImageData(tempCanvas, 0, 0)
            ctx.rect(initPosition.x, initPosition.y, width, height)
        })
        break
    }
}

const addEventListenerMousedownPosition = () => {
    window.addEventListener('mousedown', (event) => {
        initPosition.x = event.clientX
        initPosition.y = event.clientY
    })
}

const addEventListenerPopstate = () => {
    window.addEventListener('popstate', (e) => {
        canvasContext.putImageData(e.state, 0, 0)
    })
}

const draw = (action: Action) => {
    canvasContext.beginPath()
    canvasContext.lineWidth = currentSize.value * 2
    action(canvasContext)
    canvasContext.stroke()
}

const resetToolState = () => {
    tempPosition = { x: 0, y: 0 }
    isCanvasMouseDown = false
}

const resetCanvas = () => {
    const canvas = canvasContext.canvas
    canvasContext.clearRect(0, 0, canvas.width, canvas.height)
    saveCanvasToHistory()
}

const setCanvas = () => {
    let canvas = sketchpad.value!
    canvas.width = window.innerWidth
    canvas.height = window.innerHeight - 60
    let ctx = canvas.getContext('2d')
    ctx!.lineCap = 'round'
    ctx!.lineJoin = 'round'
    canvasContext = ctx!
}

const setTempCanvas = () => {
    const ctx = canvasContext
    const canvas = ctx.canvas
    tempCanvas = ctx.getImageData(0, 0, canvas.width, canvas.height)
}

const saveCanvasToHistory = () => {
    const ctx = canvasContext
    const canvas = ctx.canvas
    const tempCanvas = ctx.getImageData(0, 0, canvas.width, canvas.height)
    window.history.pushState(tempCanvas, '')
}

const getCanvasMousePosition = (clientX: number, clientY: number) => {
    const canvasPosition = canvasContext.canvas.getBoundingClientRect()
    let x = clientX - canvasPosition.x
    let y = clientY - canvasPosition.y
    return { x, y }
}

const setCanvasTempPosition = (x: number, y: number) => {
    tempPosition = { x, y }
}
</script>

<style lang="scss" scoped>
*{
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}
.draw-pad {
  width: 100%;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
