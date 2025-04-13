// SpiralChart.vue
<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'

const props = defineProps({
    id: String
})

const canvasRef = ref(null)
const circles = [
    { label: '1-2', percent: 0.8, color: '#00e396' },
    { label: '2-3', percent: 0.65, color: '#feb019' },
    { label: '4-3', percent: 0.5, color: '#ff4560' },
    { label: '4-5', percent: 0.4, color: '#775dd0' },
    { label: '5-6', percent: 0.3, color: '#3f51b5' },
    { label: '6-7', percent: 0.2, color: '#546e7a' },
    { label: '7-8', percent: 0.1, color: '#26a69a' }
]

const drawChart = () => {
    const canvas = canvasRef.value
    if (!canvas) return

    const ctx = canvas.getContext('2d')
    const centerX = canvas.width / 2
    const centerY = canvas.height / 2
    const maxRadius = Math.min(centerX, centerY) - 15
    const radiusStep = (maxRadius - 2) / circles.length

    ctx.clearRect(0, 0, canvas.width, canvas.height)

    circles.forEach((circle, index) => {
        const radius = maxRadius - index * radiusStep
        const startAngle = -Math.PI / 2
        const endAngle = startAngle + Math.PI * 2 * circle.percent

        ctx.beginPath()
        ctx.arc(centerX, centerY, radius, startAngle, endAngle)
        ctx.strokeStyle = circle.color
        ctx.lineWidth = 10
        ctx.stroke()

        ctx.beginPath()
        ctx.arc(centerX, centerY, radius, endAngle, startAngle + Math.PI * 2)
        ctx.strokeStyle = 'rgba(255, 255, 255, 0.05)'
        ctx.stroke()
    })
}

const updateCanvasSize = () => {
    const canvas = canvasRef.value
    const container = canvas.parentElement
    const rect = container.getBoundingClientRect()

    canvas.width = rect.width * 0.9
    canvas.height = rect.height * 0.9

    drawChart()
}

const resizeObserver = new ResizeObserver(updateCanvasSize)

onMounted(() => {
    resizeObserver.observe(canvasRef.value.parentElement)
    updateCanvasSize()
})

onBeforeUnmount(() => {
    resizeObserver.disconnect()
})
</script>
<template>
    <div class="chart-wrapper">
        <div class="legend">
            <div v-for="(circle, index) in circles" :key="index" class="legend-item">
                <span class="color-box" :style="{ backgroundColor: circle.color }"></span>
                {{ circle.label }}
            </div>
        </div>
        <canvas ref="canvasRef" class="spiral-canvas" />
    </div>
</template>

<style scoped>
.chart-wrapper {
    width: 90%;
    height: 90%;
    position: relative;
    background-color: #161a26;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    top: -5%;
    padding: 0;
}

.spiral-canvas {
    display: block;
    width: 90%;
    height: 90%;
    max-width: 90%;
    max-height: 90%;
    margin-top: 10px;
}

.legend {
    display: flex;
    justify-content: center;
    gap: 10px;
    font-size: 13px;
    color: #ccc;
    margin-bottom: 10px;
    flex-wrap: wrap;
}

.legend-item {
    display: flex;
    align-items: center;
    gap: 6px;
}

.color-box {
    width: 14px;
    height: 14px;
    border-radius: 3px;
    display: inline-block;
}
</style>
