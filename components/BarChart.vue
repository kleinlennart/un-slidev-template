<script setup lang="ts">
import { ref, onMounted, watch, onBeforeUnmount } from 'vue'
import * as d3 from 'd3'

interface DataPoint {
  label: string
  value: number
  lightness?: number
}

const props = defineProps({
  data: {
    type: Array as () => DataPoint[],
    default: () => [
      { label: 'Q1', value: 65, lightness: 60 },
      { label: 'Q2', value: 82, lightness: 52 },
      { label: 'Q3', value: 78, lightness: 47 },
      { label: 'Q4', value: 95, lightness: 43 },
    ],
  },
  height: {
    type: Number,
    default: 300,
  },
})

const chartRef = ref<HTMLDivElement | null>(null)
// UN Blue #009edb in HSL: hsl(197, 100%, 43%)
const UN_BLUE_HUE = 197
const UN_BLUE_SATURATION = 100

function createChart() {
  if (!chartRef.value) return

  // Clear any existing chart
  d3.select(chartRef.value).selectAll('*').remove()

  const margin = { top: 40, right: 30, bottom: 60, left: 60 }
  const width = 700 - margin.left - margin.right
  const height = props.height - margin.top - margin.bottom

  // Create SVG
  const svg = d3.select(chartRef.value)
    .append('svg')
    .attr('width', width + margin.left + margin.right)
    .attr('height', height + margin.top + margin.bottom)
    .append('g')
    .attr('transform', `translate(${margin.left},${margin.top})`)

  // Create scales
  const x = d3.scaleBand()
    .domain(props.data.map(d => d.label))
    .range([0, width])
    .padding(0.4)

  const maxValue = d3.max(props.data, d => d.value) || 100
  const y = d3.scaleLinear()
    .domain([0, maxValue])
    .range([height, 0])
    .nice()

  // Add minimal Y axis with horizontal grid lines
  const yAxis = svg.append('g')
    .call(d3.axisLeft(y)
      .ticks(5)
      .tickSize(-width)
      .tickFormat(d => d.toString()))
  
  yAxis.select('.domain').remove()
  yAxis.selectAll('.tick line')
    .style('stroke', '#e9ecef')
    .style('stroke-width', '1px')
  yAxis.selectAll('.tick text')
    .style('font-size', '13px')
    .style('fill', '#6c757d')
    .style('font-weight', '400')

  // Add X axis - minimal, no lines
  const xAxis = svg.append('g')
    .attr('transform', `translate(0,${height})`)
    .call(d3.axisBottom(x).tickSize(0))
  
  xAxis.select('.domain').remove()
  xAxis.selectAll('.tick text')
    .style('font-size', '15px')
    .style('font-weight', '600')
    .style('fill', '#212529')
    .attr('dy', '1.2em')

  // Create bars with animation
  svg.selectAll('.bar')
    .data(props.data)
    .enter()
    .append('rect')
    .attr('class', 'bar')
    .attr('x', d => x(d.label) || 0)
    .attr('width', x.bandwidth())
    .attr('y', height)
    .attr('height', 0)
    .attr('fill', d => {
      const lightness = d.lightness || 43
      return `hsl(${UN_BLUE_HUE}, ${UN_BLUE_SATURATION}%, ${lightness}%)`
    })
    .attr('rx', 6)
    .transition()
    .duration(900)
    .delay((d, i) => 200 + i * 120)
    .ease(d3.easeCubicOut)
    .attr('y', d => y(d.value))
    .attr('height', d => height - y(d.value))

  // Add value labels
  const labels = svg.selectAll('.value-label')
    .data(props.data)
    .enter()
    .append('text')
    .attr('class', 'value-label')
    .attr('x', d => (x(d.label) || 0) + x.bandwidth() / 2)
    .attr('y', height)
    .attr('text-anchor', 'middle')
    .style('fill', 'white')
    .style('font-weight', '700')
    .style('font-size', '18px')
    .style('opacity', 0)
    .text(d => d.value)

  // Animate labels
  labels.transition()
    .duration(400)
    .delay((d, i) => 800 + i * 120)
    .style('opacity', 1)
    .attr('y', d => y(d.value) + 26)
}

onMounted(() => {
  createChart()
  
  // Watch for slide changes in Slidev
  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          // Re-animate when slide becomes visible
          createChart()
        }
      })
    },
    { threshold: 0.1 }
  )

  if (chartRef.value) {
    observer.observe(chartRef.value)
  }

  onBeforeUnmount(() => {
    observer.disconnect()
  })
})

</script>

<template>
  <div class="bar-chart-container">
    <div ref="chartRef" class="chart"></div>
  </div>
</template>

<style scoped>
.bar-chart-container {
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
  padding: 1rem 0;
}

.chart {
  display: flex;
  justify-content: center;
}

.chart :deep(svg) {
  overflow: visible;
}
</style>
