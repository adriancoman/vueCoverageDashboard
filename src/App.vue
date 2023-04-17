<script setup>
import {ref, onMounted} from 'vue'
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

</script>
<template>

    <ChartComponent/>

    <br>

    <div>

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