<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

const padding = 30

const data = [58, 90, 95, 90, 35, 100, 80, 95, 50, 85, 95, 90, 75, 70, 15, 15, 40, 45, 90, 10]
const yValues = [28, 58, 109]

const svgRef = ref(null)
const svgWidth = ref(400)
const svgHeight = ref(200)

const getX = (i, width) => {
    const totalWidth = width - padding * 2
    return padding + (i / 21) * totalWidth
}

const getY = (val, height) => {
    const maxVal = 109
    const minVal = 0
    const range = maxVal - minVal
    return padding + ((maxVal - val) / range) * (height - padding * 2)
}

const linePoints = computed(() => {
    return data.map((val, i) => `${getX(i + 1, svgWidth.value)},${getY(val, svgHeight.value)}`).join(' ')
})

const resizeObserver = new ResizeObserver(() => {
    if (svgRef.value) {
        const width = svgRef.value.parentElement.offsetWidth
        const height = svgRef.value.parentElement.offsetHeight
        svgWidth.value = width
        svgHeight.value = height
    }
})

onMounted(() => {
    resizeObserver.observe(svgRef.value.parentElement)
})

onBeforeUnmount(() => {
    resizeObserver.disconnect()
})
</script>

<template>
    <div class="line-chart-container">
        <svg
            ref="svgRef"
            :viewBox="`0 0 ${svgWidth} ${svgHeight}`"
            preserveAspectRatio="xMidYMid meet"
            width="80%"
            height="80%"
        >
            <!-- Grid lines -->
            <g class="grid-lines">
                <line
                    v-for="(y, i) in yValues"
                    :key="'y'+i"
                    :x1="padding"
                    :x2="svgWidth - padding"
                    :y1="getY(y, svgHeight)"
                    :y2="getY(y, svgHeight)"
                    stroke="black"
                />

                <line
                    v-for="i in 22"
                    :key="'x'+i"
                    :x1="getX(i - 1, svgWidth)"
                    :x2="getX(i - 1, svgWidth)"
                    :y1="padding"
                    :y2="svgHeight - padding"
                    stroke="black"
                />
            </g>

            <line
                :x1="padding"
                :y1="padding"
                :x2="padding"
                :y2="svgHeight - padding"
                stroke="#aaa"
                stroke-width="1.5"
            />

            <line
                :x1="padding"
                :y1="svgHeight - padding"
                :x2="svgWidth - padding"
                :y2="svgHeight - padding"
                stroke="#aaa"
                stroke-width="1.5"
            />

            <text
                v-for="(y, i) in yValues"
                :key="'ylabel'+i"
                :x="padding - 10"
                :y="getY(y, svgHeight) + 4"
                font-size="20"
                fill="#fff"
                text-anchor="end"
            >
                {{ y }}
            </text>

            <text
                v-for="i in 20"
                :key="'xlabel'+i"
                :x="getX(i, svgWidth)"
                :y="svgHeight - padding + 15"
                font-size="20"
                fill="#fff"
                text-anchor="middle"
            >
                {{ i - 1 }}
            </text>

            <text
                :x="padding - 10"
                :y="svgHeight - padding + 15"
                font-size="20"
                fill="#fff"
                text-anchor="end"
            >
                -2
            </text>

            <polyline
                :points="linePoints"
                fill="none"
                stroke="#00e396"
                stroke-width="2"
                stroke-linejoin="round"
            />

            <circle
                v-for="(val, i) in data"
                :key="'point'+i"
                :cx="getX(i + 1, svgWidth)"
                :cy="getY(val, svgHeight)"
                r="3"
                fill="#00e396"
            />
        </svg>
    </div>
</template>

<style scoped>
.line-chart-container {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
    background-color: #161a26;
    padding: 0;
    box-sizing: border-box;
    overflow: hidden;
}
</style>
