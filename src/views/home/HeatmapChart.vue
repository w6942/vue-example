<script setup lang="ts">
import * as echarts from 'echarts/core'
import {
  CalendarComponent,
  TitleComponent,
  TooltipComponent,
  VisualMapComponent
} from 'echarts/components'
import type {
  CalendarComponentOption,
  TitleComponentOption,
  TooltipComponentOption,
  VisualMapComponentOption
} from 'echarts/components'
import { HeatmapChart, type HeatmapSeriesOption } from 'echarts/charts'
import { CanvasRenderer } from 'echarts/renderers'
import { onMounted, ref } from 'vue'

echarts.use([
  TitleComponent,
  CalendarComponent,
  TooltipComponent,
  VisualMapComponent,
  HeatmapChart,
  CanvasRenderer
])

type EchartsOption = echarts.ComposeOption<
  | TitleComponentOption
  | CalendarComponentOption
  | TooltipComponentOption
  | VisualMapComponentOption
  | HeatmapSeriesOption
>

const chartDomRef = ref<HTMLDivElement>()
let chart: echarts.EChartsType
const option: EchartsOption = {
  title: {
    top: 30,
    left: 'center',
    text: 'Daily Step Count'
  },
  tooltip: {},
  visualMap: {
    min: 0,
    max: 10000,
    type: 'piecewise',
    orient: 'horizontal',
    left: 'center',
    top: 65
  },
  calendar: {
    top: 120,
    left: 30,
    right: 30,
    cellSize: ['auto', 13],
    range: '2016',
    itemStyle: {
      borderWidth: 0.5
    },
    yearLabel: { show: false }
  },
  series: {
    type: 'heatmap',
    coordinateSystem: 'calendar',
    data: getVirtualData('2016')
  }
}

onMounted(() => {
  chart = echarts.init(chartDomRef.value)
  chart.setOption(option)
})

function getVirtualData(year: string) {
  const date = +echarts.time.parse(year + '-01-01')
  const end = +echarts.time.parse(+year + 1 + '-01-01')
  const dayTime = 3600 * 24 * 1000
  const data: [string, number][] = []
  for (let time = date; time < end; time += dayTime) {
    data.push([
      echarts.time.format(time, '{yyyy}-{MM}-{dd}', false),
      Math.floor(Math.random() * 10000)
    ])
  }
  return data
}
</script>

<template>
  <div ref="chartDomRef" class="w-full h-full"></div>
</template>

<style scoped></style>
