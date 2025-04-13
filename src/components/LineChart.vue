<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

const padding = 30
const data = [58, 90, 95, 90, 35, 100, 80, 95, 50, 85, 95, 90, 75, 70, 15, 15, 40, 45, 90, 10]
const yValues = [28, 58, 109]

const svgRef = ref(null)
const svgWidth = ref(400)
const svgHeight = ref(200)
const containerRef = ref(null)

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

const tooltip = ref({ show: false, x: 0, y: 0, value: null })

const showTooltip = (event, val) => {
    const svgRect = svgRef.value.getBoundingClientRect()
    const containerRect = containerRef.value.getBoundingClientRect()

    tooltip.value = {
        show: true,
        x: event.clientX - containerRect.left,
        y: event.clientY - containerRect.top,
        value: val,
    }
}

const hideTooltip = () => {
    tooltip.value.show = false
}

const showTooltipFromIndex = (i, val) => {
    if (!svgRef.value || !containerRef.value) return

    const svgRect = svgRef.value.getBoundingClientRect()
    const containerRect = containerRef.value.getBoundingClientRect()

    // SVG ichidagi nuqta koordinatalari
    const svgX = getX(i + 1, svgWidth.value)
    const svgY = getY(val, svgHeight.value)

    // SVG viewBox transformatsiyasini hisobga olish
    const scaleX = svgRect.width / svgWidth.value
    const scaleY = svgRect.height / svgHeight.value

    // Container nisbatida pozitsiya
    tooltip.value = {
        show: true,
        x: (svgX * scaleX) + (svgRect.left - containerRect.left),
        y: (svgY * scaleY) + (svgRect.top - containerRect.top),
        value: val
    }
}

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
    <div class="line-chart-container" ref="containerRef">
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
                    stroke="rgba(255,255,255,0.2)"
                />

                <line
                    v-for="i in 22"
                    :key="'x'+i"
                    :x1="getX(i - 1, svgWidth)"
                    :x2="getX(i - 1, svgWidth)"
                    :y1="padding"
                    :y2="svgHeight - padding"
                    stroke="rgba(255,255,255,0.2)"
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
                font-size="15"
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
                font-size="15"
                fill="#fff"
                text-anchor="middle"
            >
                {{ i - 1 }}
            </text>

            <text
                :x="padding - 10"
                :y="svgHeight - padding + 15"
                font-size="15"
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
                r="5"
                fill="#00e396"
                @mouseover="event => showTooltipFromIndex(i, val)"
                @mousemove="event => showTooltipFromIndex(i, val)"
                @mouseout="hideTooltip"
                style="cursor: pointer;"
            />

        </svg>

        <div
            v-if="tooltip.show"
            class="tooltip"
            :style="{
                top: tooltip.y + 'px',
                left: tooltip.x + 'px',
                transform: 'translate(-50%, -100%)'
            }"
        >
            {{ tooltip.value }}
        </div>
    </div>
</template>

<style scoped>
.line-chart-container {
    position: relative;
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

.tooltip {
    position: absolute;
    background: white;
    color: black;
    font-size: 14px;
    font-weight: bold;
    border-radius: 4px;
    padding: 20px 40px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
    pointer-events: none;
    white-space: nowrap;
    z-index: 100;
}

.tooltip:after {
    content: '';
    position: absolute;
    top: 100%;
    left: 50%;
    margin-left: -5px;
    border-width: 5px;
    border-style: solid;
    border-color: rgba(0, 0, 0, 0.8) transparent transparent transparent;
}
</style>