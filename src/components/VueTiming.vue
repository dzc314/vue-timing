<script setup>
import { ref, computed, onMounted } from 'vue'
const emit = defineEmits(['timing-click'])
const { listData = [], columnData = [] } = defineProps(['listData', 'columnData'])
const activeEventIndex = ref(null)

// 向外暴露点击事件
const timingHandler = (data, index) => {
  activeEventIndex.value = index
  emit('timing-click', data)
}
// 计算方向
const calcDirection = (data) => {
  const { from, to } = data
  const fromIndex = columnData.indexOf(from)
  const toIndex = columnData.indexOf(to)
  return fromIndex < toIndex ? 'arrow-right' : 'arrow-left'
}

const size = computed(() => {
  return Math.max(columnData.length - 1, 0)
})
// 计算尺寸与偏移量
const calcStyle = (data) => {
  const { from, to } = data
  const fromIndex = columnData.indexOf(from)
  const toIndex = columnData.indexOf(to)
  const index = Math.min(fromIndex, toIndex)
  const len = Math.abs(fromIndex - toIndex)
  const width = formatPercentage(len / size.value)
  const marginLeft = formatPercentage(index / size.value)
  return {
    width,
    marginLeft
  }
}

const timingListMarginRight = ref(0)
// 小数转百分比
const formatPercentage = (value) => {
  return Math.round(value * 10000) / 100 + '%'
}

const timingView = ref()
const myObserver = new ResizeObserver((entries) => {
  // 判断timingView是否出现滚动条，对数据展示区做特殊处理
  if (timingView.value.scrollHeight > timingView.value.clientHeight) {
    timingListMarginRight.value = '-10px'
  } else {
    timingListMarginRight.value = 0
  }
})

onMounted(() => {
  myObserver.observe(timingView.value)
})
</script>
<template>
  <div class="timing-diagram" @click="activeEventIndex = null">
    <div class="timing-items">
      <div class="timing-item" v-for="item in columnData" :key="item">
        <strong>{{ item }}</strong>
      </div>
    </div>
    <div class="timing-view" ref="timingView">
      <div :style="{ 'margin-right': timingListMarginRight }">
        <div
          class="timing-row"
          v-for="(item, index) in listData"
          :key="index"
          :class="{ active: index === activeEventIndex }"
        >
          <p
            @click.stop="timingHandler(item, index)"
            class="timing-row-item"
            :class="calcDirection(item)"
            :style="calcStyle(item)"
          >
            {{ item.text }}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
$event-line-color: #666;
.timing-diagram {
  position: relative;
  height: 100%;
  min-height: 400px;
  height: 400px;
  padding: 50px;
  overflow: hidden;
  .timing-items {
    display: flex;
    height: 100%;
    justify-content: space-between;
    .timing-item {
      position: relative;
      width: 0;
      border-right: 1px dashed #aaa;
      > strong {
        position: absolute;
        bottom: 100%;
        left: 50%;
        width: 100px;
        margin-left: -50px;
        text-align: center;
        word-wrap: break-word;
        word-break: normal;
      }
    }
  }
  .timing-view {
    position: absolute;
    top: 50px;
    left: 0;
    width: 100%;
    height: calc(100% - 100px);
    overflow-x: hidden;
    overflow-y: auto;
    box-sizing: border-box;
    .timing-row {
      padding: 10px 50px;
      &.active {
        color: rgba(255, 120, 15, 0.8);
      }
      > .timing-row-item {
        margin: 0;
        position: relative;
        border-bottom: 1px solid $event-line-color;
        cursor: pointer;
        text-align: center;

        &::after {
          content: '';
          position: absolute;
          bottom: -4px;
          display: block;
          width: 0;
          height: 0;
          border-top: 4px solid transparent;
          border-bottom: 4px solid transparent;
        }
        &.arrow-right::after {
          right: 0;
          border-left: 6px solid $event-line-color;
        }
        &.arrow-left::after {
          left: 0;
          border-right: 6px solid $event-line-color;
        }
      }
    }
  }
  // 滚动条
  ::-webkit-scrollbar {
    width: 10px;
    height: 10px;
  }
  ::-webkit-scrollbar-track {
    background: transparent;
    border-radius: 2px;
  }
  ::-webkit-scrollbar-thumb {
    background: rgba(197, 206, 224, 0.5);
    border-radius: 10px;
  }
  ::-webkit-scrollbar-thumb:hover {
    background: rgba(255, 120, 15, 0.8);
  }
  ::-webkit-scrollbar-corner {
    background: transparent;
  }
}
</style>
