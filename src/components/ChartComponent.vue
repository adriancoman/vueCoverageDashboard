<script setup>
import Chart from 'chart.js/auto';
import {onMounted, ref} from 'vue'
import {supabase} from '@/supabase'

const coverageList = ref([])
const selectedRange = ref("all")
const dataType = ref("coverage")

async function getCoverage() {
    const {data} = await supabase.from('codecoverage').select()
    coverageList.value = data
    console.log("got data")
    drawChart()
}

function drawChart() {

    const today = new Date()
    let filteredList

    switch (selectedRange.value) {
        case 'week':
            const sevenDaysAgo = new Date(today.getFullYear(), today.getMonth(), today.getDate() - 7)
            filteredList = coverageList.value.filter(item => new Date(item.created_at) >= sevenDaysAgo)
            break
        case 'month':
            const thirtyDaysAgo = new Date(today.getFullYear(), today.getMonth(), today.getDate() - 30)
            filteredList = coverageList.value.filter(item => new Date(item.created_at) >= thirtyDaysAgo)
            break
        case '3month':
            const threeMonthsAgo = new Date(today.getFullYear(), today.getMonth() - 3, today.getDate())
            filteredList = coverageList.value.filter(item => new Date(item.created_at) >= threeMonthsAgo)
            break
        default:
            filteredList = coverageList.value
            break
    }
    filteredList.sort((a, b) => new Date(a.created_at) - new Date(b.created_at))

    //create dates
    const dateMap = filteredList.reduce((acc, item) => {
        const date = new Date(item.created_at).toLocaleDateString('en-GB', {day: 'numeric', month: 'short'})
        if (!acc[date]) {
            acc[date] = []
        }
        acc[date].push(item)
        return acc
    }, {})

    const uniqueDates = Object.keys(dateMap)
    const testCountList = uniqueDates.map(date => {
        const items = dateMap[date]
        return items.reduce((max, item) => Math.max(max, item.test_count), 0)
    })

    const codeCoverageList = uniqueDates.map(date => {
        const items = dateMap[date]
        return items.reduce((max, item) => Math.max(max, item.code_coverage), 0)
    })

    let toDisplayData
    let yAxisLabel
    if (dataType.value === "coverage") {
        toDisplayData = {
            label: 'Code coverage',
            data: codeCoverageList,
            fill: false,
            cubicInterpolationMode: 'monotone',
            tension: 0.4,
            pointStyle: 'circle',
            pointRadius: 5,
            pointHoverRadius: 7.5,
            borderColor: '#2CC84D',
            backgroundColor: '#2e9d47',
        }
        yAxisLabel = "Code coverage (%)"
    } else {
        toDisplayData = {
            label: 'Unit tests',
            data: testCountList,
            fill: false,
            cubicInterpolationMode: 'monotone',
            tension: 0.4,
            pointStyle: 'circle',
            pointRadius: 5,
            pointHoverRadius: 7.5,
            borderColor: '#FF6384',
            backgroundColor: '#FFB1C1',
        }
        yAxisLabel = "Unit tests (#)"
    }

    const ctx = document.getElementById('myChart');
    const config = {
        type: 'line',
        data: {
            labels: uniqueDates,
            datasets: [
                toDisplayData
            ],
        },
        options: {
            responsive: true,
            plugins: {
                title: {
                    display: false,
                },
                legend: {
                    display: false
                },
            },
            interaction: {
                intersect: false,
            },

            scales: {
                x: {
                    display: true,
                    title: {
                        display: true,
                        text: 'Time',
                        color: '#ffffff',
                    },
                    grid: {
                        display: false,
                        color: '#3a2e4b'
                    },
                    ticks: {
                        color: '#fff'
                    },
                },
                y: {
                    display: true,
                    title: {
                        display: true,
                        text: yAxisLabel,
                        color: '#ffffff',
                    },
                    grid: {
                        display: false,
                        color: '#3a2e4b'
                    },
                    ticks: {
                        color: '#fff'
                    },
                    suggestedMin: 0,
                    beginAtZero: true,

                }
            }
        },
    };

    // check if previous draw exists and delete it
    let chartStatus = Chart.getChart("myChart");
    if (chartStatus !== undefined) {
        chartStatus.destroy();
    }

    // draw
    new Chart(ctx, config);
}

function selectRange(range) {
    this.selectedRange = range
    getCoverage()
}

function selectCount(dataType) {
    this.dataType = dataType
    getCoverage()
}

onMounted(() => {
    getCoverage()
})

</script>


<template>
    <div class="coverage-chart">
        <div class="date-range">
            <div class="buttons">
                <button :class="{ 'active-first': selectedRange === 'week' }" class="first-button"
                        @click="selectRange('week')">7 days
                </button>
                <button :class="{ active: selectedRange === 'month' }" @click="selectRange('month')">30 days</button>
                <button :class="{ active: selectedRange === '3month' }" @click="selectRange('3month')">3 months</button>
                <button :class="{ 'active-last': selectedRange === 'all' }" class="last-button"
                        @click="selectRange('all')">All
                </button>
            </div>
        </div>
        <div class="date-range">
            <div class="buttons">
                <button :class="{ 'active-first': dataType === 'coverage' }" class="first-button"
                        @click="selectCount('coverage')">Test coverage
                </button>
                <button :class="{ 'active-last': dataType === 'count' }" class="last-button"
                        @click="selectCount('count')">Test count
                </button>
            </div>
        </div>
        <div class="chart">
            <canvas id="myChart"></canvas>
        </div>
    </div>
</template>
