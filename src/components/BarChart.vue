<script setup>
import { ref, computed } from 'vue'

const data = [58, 90, -50, 20, -100, 60, 100, -70, 35, 0, 80, -85, 10, -40, 75, 90, -30, 25, 100, -90]
const yValues = [-100, -50, 0, 50, 100]

// Fixed viewBox o'lchamlari
const viewBoxWidth = 400
const viewBoxHeight = 200
const padding = 30
const barGap = 2

const barWidth = computed(() => {
    const totalWidth = viewBoxWidth - padding * 2
    return (totalWidth / data.length) - barGap
})

const getX = (i) => {
    return padding + i * (barWidth.value + barGap)
}

const getY = (val) => {
    const range = 200 // -100 dan +100 gacha
    return padding + ((100 - val) / range) * (viewBoxHeight - padding * 2)
}

const getBarHeight = (val) => {
    const zeroY = getY(0)
    const valY = getY(val)
    return Math.abs(zeroY - valY)
}
</script>

<template>
    <div class="bar-chart-container">
        <svg
            viewBox="0 0 400 200"
            preserveAspectRatio="xMidYMid meet"
            class="chart-svg"
        >
            <!-- Grid lines -->
            <g class="grid-lines">
                <line
                    v-for="(y, i) in yValues"
                    :key="'yline'+i"
                    :x1="padding"
                    :x2="viewBoxWidth - padding"
                    :y1="getY(y)"
                    :y2="getY(y)"
                    stroke="rgba(255,255,255,0.15)"
                />
            </g>

            <!-- Y o'qi -->
            <line
                :x1="padding"
                :y1="padding"
                :x2="padding"
                :y2="viewBoxHeight - padding"
                stroke="#aaa"
                stroke-width="1.5"
            />

            <!-- X o'qi -->
            <line
                :x1="padding"
                :y1="getY(0)"
                :x2="viewBoxWidth - padding"
                :y2="getY(0)"
                stroke="#aaa"
                stroke-width="1.5"
            />

            <!-- Y labels -->
            <text
                v-for="(y, i) in yValues"
                :key="'ylabel'+i"
                :x="padding - 10"
                :y="getY(y) + 4"
                font-size="10"
                fill="#fff"
                text-anchor="end"
            >
                {{ y }}
            </text>

            <!-- X labels -->
            <text
                v-for="i in data.length"
                :key="'xlabel'+i"
                :x="getX(i-1) + barWidth / 2"
                :y="viewBoxHeight - padding + 15"
                font-size="10"
                fill="#fff"
                text-anchor="middle"
            >
                {{ i }}
            </text>

            <!-- Barlar -->
            <rect
                v-for="(val, i) in data"
                :key="'bar'+i"
                :x="getX(i)"
                :y="val >= 0 ? getY(val) : getY(0)"
                :width="barWidth"
                :height="getBarHeight(val)"
                :fill="val >= 0 ? '#00e396' : '#ff4560'"
                rx="2"
                ry="2"
            />
        </svg>
    </div>
</template>

<style scoped>
.bar-chart-container {
    width: 100%;
    height: 100%;
    background-color: #161a26;
    border-radius: 1rem;
    overflow: hidden;
    position: relative;
}

.chart-svg {
    width: 100%;
    height: 100%;
    object-fit: contain;
    display: block;
}
</style>