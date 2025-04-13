<script setup>
import { ref, watchEffect, onMounted, onUnmounted } from 'vue'
import { GridLayout, GridItem } from 'vue3-grid-layout'
import SparkLineChart from '@/components/SparkLine.vue'
import SpiralChart from '@/components/SpiralChart.vue'
import LineChart from "@/components/LineChart.vue"
import BarChart from "@/components/BarChart.vue";

const layout = ref([])

const defaultLayout = [
    { i: '1', x: 0, y: 0, w: 3, h: 4, name: 'Spark Line', static: true },
    { i: '2', x: 0, y: 0, w: 3, h: 8, name: 'Spiral Chart' },
    { i: '3', x: 3, y: 0, w: 9, h: 12, name: 'Line Chart' },
    { i: '4', x: 0, y: 4, w: 12, h: 12, name: 'Bar Chart' },
]

const mobileLayout = [
    { i: '1', x: 0, y: 0, w: 12, h: 3, name: 'Spark Line', static: true },
    { i: '2', x: 0, y: 0, w: 12, h: 5, name: 'Spiral Chart' },
    { i: '3', x: 0, y: 0, w: 12, h: 6, name: 'Line Chart' },
    { i: '4', x: 0, y: 0, w: 12, h: 8, name: 'Bar Chart' },
]

watchEffect(() => {
    if (window.innerWidth < 978) {
        layout.value = mobileLayout
    } else {
        layout.value = defaultLayout
    }
})

const updateLayout = () => {
    layout.value = window.innerWidth < 978 ? mobileLayout : defaultLayout
}

onMounted(() => {
    updateLayout()
    window.addEventListener('resize', updateLayout)
})

onUnmounted(() => {
    window.removeEventListener('resize', updateLayout)
})


</script>

<template>
    <div class="container">
        <div class="grid">
            <GridLayout
                v-model:layout="layout"
                :col-num="12"
                :row-height="30"
                :is-draggable="true"
                :is-resizable="true"
                :vertical-compact="true"
                :margin="[10, 10]"
                :use-css-transforms="true"
                style="min-height: 100vh"
            >
                <GridItem
                    v-for="item in layout"
                    :key="item.i"
                    :x="item.x"
                    :y="item.y"
                    :w="item.w"
                    :h="item.h"
                    :i="item.i"
                    :static="item.static || false"
                >
                    <div class="border">
                        <div class="block-header">{{ item.name }}</div>
                        <div class="block-body">
                            <SparkLineChart v-if="item.i === '1'" :id="item.i" />
                            <SpiralChart v-if="item.i === '2'" :id="item.i" />
                            <LineChart v-if="item.i === '3'" :id="item.i" />
                            <BarChart v-if="item.i === '4'" :id="item.i" />
                        </div>
                    </div>
                </GridItem>
            </GridLayout>
        </div>
    </div>
</template>

<style scoped>
.container {
    background: #161a26;
    padding: 1rem;
}

@media (min-width: 1024px) {
    .container {
        padding: 2rem 4rem;
    }
}

.grid {
    background: black;
    border-radius: 0.5rem;
    overflow: hidden;
}

.border {
    height: 100%;
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    background-color: #161a26;
    color: white;
    text-align: center;
    border-radius: 0.5rem;
    overflow: hidden;
}

.block-header {
    background: #1e1e2f;
    padding: 8px;
    font-weight: bold;
    font-size: 14px;
    text-align: center;
    border-radius: 0.5rem 0.5rem 0 0;
}

.block-body {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 0;
    overflow: hidden;
}
</style>