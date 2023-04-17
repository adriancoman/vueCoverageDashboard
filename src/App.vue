<script setup>
import {ref, onMounted, computed} from 'vue'
import {supabase} from './supabase'

import ChartComponent from './components/ChartComponent.vue'

const coverageList = ref([])

async function getCoverage() {
    const {data} = await supabase.from('codecoverage').select()
    coverageList.value = data
}

onMounted(() => {
    getCoverage()
})

const formatDate = (dateStr) => {
    const dateObj = new Date(dateStr)
    const year = dateObj.getFullYear()
    const month = dateObj.getMonth() + 1
    const day = dateObj.getDate()
    const hours = dateObj.getHours()
    const minutes = dateObj.getMinutes()
    const seconds = dateObj.getSeconds()

    return `${year}-${month.toString().padStart(2, "0")}-${day.toString().padStart(2, "0")} ${hours.toString().padStart(2, "0")}:${minutes.toString().padStart(2, "0")}:${seconds.toString().padStart(2, "0")}`
}

const highestCoverage = computed(() => {
    if (coverageList.value.length === 0) {
        return 0
    }

    return Math.max(...coverageList.value.map((item) => item.code_coverage))
})


</script>
<template>

    <ChartComponent/>

    <br>

    <div>
        <div class="top-section">
            <p>The highest code coverage value is: {{ highestCoverage }}</p>
        </div>

        <div class="container">
            <Bar v-if="loaded" :data="chartData"/>
        </div>

        <table>
            <thead>
            <tr>
                <th>Index</th>
                <th>Created at</th>
                <th>Coverage</th>
                <th>Test count</th>
                <th>Pull request</th>
                <th>Author</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="(coverageData, index) in coverageList" :key="coverageData.uuid">
                <td>{{ index + 1 }}</td>
                <td>{{ formatDate(coverageData.created_at) }}</td>
                <td>{{ coverageData.code_coverage }}</td>
                <td>{{ coverageData.test_count }}</td>
                <td>{{ coverageData.pull_request }}</td>
                <td>{{ coverageData.author }}</td>
            </tr>
            </tbody>
        </table>
    </div>
</template>