<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

const padding = 30
const data = [58, 90, -50, 20, -100, 60, 100, -70, 35, 0, 80, -85, 10, -40, 75, 90, -30, 25, 100, -90]
const yValues = [-100, -50, 0, 50, 100]

const svgRef = ref(null)
const svgWidth = ref(400)
const svgHeight = ref(200)

const getX = (i, width) => {
    const totalWidth = width - padding * 2
    return padding + (i / 21) * totalWidth
}

const getY = (val, height) => {
    const maxVal = 100
    const minVal = -100
    const range = maxVal - minVal
    return padding + ((maxVal - val) / range) * (height - padding * 2)
}

// Bar diagramma uchun hisob-kitoblar
const barWidth = computed(() => {
    const totalWidth = svgWidth.value - padding * 2
    return (totalWidth / data.length) / 2.5
})

const barGap = 2

const getBarX = (i, isSecondBar = false) => {
    const baseX = getX(i + 1, svgWidth.value) - barWidth.value / 2
    return isSecondBar ? baseX + barWidth.value + barGap : baseX
}

const getBarHeight = (val, height) => {
    const zeroY = getY(0, height)
    const valY = getY(val, height)
    return Math.abs(zeroY - valY)
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
            width="100%"
            height="100%"
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

            <!-- Y o'qi -->
            <line
                :x1="padding"
                :y1="padding"
                :x2="padding"
                :y2="svgHeight - padding"
                stroke="#aaa"
                stroke-width="1.5"
            />

            <!-- X o'qi -->
            <line
                :x1="padding"
                :y1="getY(0, svgHeight)"
                :x2="svgWidth - padding"
                :y2="getY(0, svgHeight)"
                stroke="#aaa"
                stroke-width="1.5"
            />

            <!-- Y labels -->
            <text
                v-for="(y, i) in yValues"
                :key="'ylabel'+i"
                :x="padding - 10"
                :y="getY(y, svgHeight) + 4"
                font-size="10"
                fill="#fff"
                text-anchor="end"
            >
                {{ y }}
            </text>

            <!-- X labels -->
            <text
                v-for="i in 20"
                :key="'xlabel'+i"
                :x="getX(i, svgWidth)"
                :y="svgHeight - padding + 15"
                font-size="15"
                fill="#fff"
                text-anchor="middle"
            >
                {{ i - 1 }}
            </text>

            <!-- Bar diagrammalar -->
            <g class="bars">
                <!-- Birinchi bar (yashil) -->
                <rect
                    v-for="(val, i) in data"
                    :key="'bar1-'+i"
                    :x="getBarX(i)"
                    :y="val >= 0 ? getY(val, svgHeight) : getY(0, svgHeight)"
                    :width="barWidth"
                    :height="getBarHeight(val, svgHeight)"
                    fill="#00e396"
                    rx="2"
                    ry="2"
                    opacity="0.7"
                />

                <!-- Ikkinchi bar (moviy) -->
                <rect
                    v-for="(val, i) in data"
                    :key="'bar2-'+i"
                    :x="getBarX(i, true)"
                    :y="getY(0, svgHeight)"
                    :width="barWidth"
                    :height="getBarHeight(val / 2, svgHeight)"
                fill="#008ffb"
                rx="2"
                ry="2"
                opacity="0.7"
                />
            </g>
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
    margin: 30px;
    box-sizing: border-box;
    overflow: hidden;
    border-radius: 0.5rem;
}
</style>