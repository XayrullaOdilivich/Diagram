<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import VueApexCharts from "vue3-apexcharts"

const pointCount = 30
const initialValue = 20
const initialData = Array.from({ length: pointCount }, () => initialValue)

const series = ref([{
    name: 'Growth',
    data: initialData
}])

const chartOptions = ref({
    chart: {
        type: 'line',
        sparkline: { enabled: true },
        animations: {
            enabled: true,
            easing: 'linear',
            speed: 400,
            dynamicAnimation: {
                enabled: true,
                speed: 300
            }
        }
    },
    stroke: {
        curve: 'straight',
        width: 2
    },
    colors: ['#8884d8'],
    tooltip: { enabled: false },
    markers: {
        size: [ ...Array(pointCount - 1).fill(0), 6 ], // faqat oxirgi nuqtada marker bo'ladi
        colors: [],
        strokeColors: '#000',
        strokeWidth: 2
    },
    yaxis: {
        min: 10,  // Y-aksisining minimal qiymati
        max: 40,  // Y-aksisining maksimal qiymati
    }
})

let intervalId

onMounted(() => {
    intervalId = setInterval(() => {
        const currentData = [...series.value[0].data]

        // Yangi random qiymat, farqni kichik qilamiz (max -min = 3 masalan)
        const delta = Math.floor(Math.random() * 7) - 3  // 0 va 3 orasida o'zgarish
        const prevValue = currentData[currentData.length - 1]
        const newValue = prevValue + delta

        // O'zgarishlar kichikroq bo'lishi kerak
        if (newValue < 10) return // minimal qiymatdan pastga tushmasin
        if (newValue > 40) return // maksimal qiymatdan oshmasin

        currentData.shift()
        currentData.push(newValue)

        // Nuqta yuqoriga ketdimi yoki pastgami?
        const lastDotColor = newValue > prevValue ? '#00e396' : '#ff4560'

        chartOptions.value.markers.colors = [
            ...Array(pointCount - 1).fill('transparent'), // qolgan nuqtalar ko‘rinmaydi
            lastDotColor
        ]

        series.value = [{
            name: 'Growth',
            data: currentData
        }]
    }, 1000)
})

onBeforeUnmount(() => {
    clearInterval(intervalId)
})
</script>

<template>
    <apexchart
        type="line"
        height="100%"
        :options="chartOptions"
        :series="series"
    />
</template>
