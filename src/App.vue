<script setup>
import { ref } from 'vue'
import VueTiming from './components/VueTiming.vue'
function getRandomInt(min, max, excludeInt) {
  const result = Math.floor(Math.random() * (max - min + 1)) + min
  if (result === excludeInt) {
    return getRandomInt(min, max, excludeInt)
  }
  return result
}
const getRandomColumn = (columnData) => {
  const fromIndex = getRandomInt(0, 3)
  const toIndex = getRandomInt(0, 3, fromIndex)
  const from = columnData[fromIndex]
  const to = columnData[toIndex]
  const text = `from ${from} to ${to}`
  return {
    from,
    to,
    text
  }
}
const columnData = ref(['A', 'B', 'C', 'D'])
const listData = ref([])
listData.value = new Array(10).fill('').map(() => getRandomColumn(columnData.value))
setTimeout(() => {
  const newData = new Array(15).fill('').map(() => getRandomColumn(columnData.value))
  listData.value = listData.value.concat(newData)
}, 2000)
const clickHandler = (data) => {
  console.log('clickHandler', data)
}
</script>

<template>
  <VueTiming
    class="view"
    :column-data="columnData"
    :list-data="listData"
    @timing-click="clickHandler"
  ></VueTiming>
</template>

<style scoped>
.view {
  /* height: 800px; */
}
</style>
