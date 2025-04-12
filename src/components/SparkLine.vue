<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const canvasRef = ref(null)
let ctx
const width = 300
const height = 80
const points = []
const maxPoints = 50
let animationFrameId

function getRandomY(lastY) {
    let change = Math.floor(Math.random() * 60) - 30
    let newY = lastY + change
    if (newY < 10) newY = 10
    if (newY > height - 10) newY = height - 10
    return newY
}

function draw() {
    ctx.clearRect(0, 0, width, height)

    // Pastki bo'limni yashil shaffof rang bilan to'ldirish
    ctx.beginPath()
    ctx.moveTo(0, points[0])
    for (let i = 1; i < points.length; i++) {
        let x = (i / maxPoints) * width
        ctx.lineTo(x, points[i])
    }
    ctx.lineTo(width, height)        // oxirgi nuqtadan pastga tush
    ctx.lineTo(0, height)            // chap pastki burchakka chiz
    ctx.closePath()
    ctx.fillStyle = 'rgba(0, 227, 150, 0.15)' // yashil shaffof fon
    ctx.fill()

    // Egri chiziqni chizish
    ctx.beginPath()
    ctx.moveTo(0, points[0])
    for (let i = 1; i < points.length; i++) {
        let x = (i / maxPoints) * width
        ctx.lineTo(x, points[i])
    }
    ctx.strokeStyle = '#00e396'  // chiziq rangi
    ctx.lineWidth = 2
    ctx.stroke()

    // Nuqta (oxirgisi)
    const lastY = points[points.length - 1]
    const prevY = points[points.length - 2]
    ctx.beginPath()
    ctx.arc(width, lastY, 4, 0, Math.PI * 2)
    ctx.fillStyle = lastY < prevY ? '#00e396' : '#ff4d4f'
    ctx.fill()
}

function update() {
    const lastY = points[points.length - 1] || height / 2
    const newY = getRandomY(lastY)
    points.push(newY)
    if (points.length > maxPoints) {
        points.shift()
    }

    draw()
    animationFrameId = requestAnimationFrame(() => {
        setTimeout(update, 400)
    })
}

onMounted(() => {
    const canvas = canvasRef.value
    canvas.width = width
    canvas.height = height
    ctx = canvas.getContext('2d')

    // boshlanishda egri chiziq yasash uchun tasodifiy qiymatlar bilan to‘ldiramiz
    for (let i = 0; i < maxPoints; i++) {
        const lastY = points.length > 0 ? points[points.length - 1] : height / 2
        points.push(getRandomY(lastY))
    }

    update()
})

onUnmounted(() => {
    cancelAnimationFrame(animationFrameId)
})
</script>

<template>
    <canvas ref="canvasRef" style="width: 100%; height: 100%; display: block;" />
</template>

<style scoped>
canvas {
    background-color: #161a26;
    border-radius: 6px;
}
</style>
