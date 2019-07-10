<template>
  <div class="app-container">
    <div
      ref="bar"
      class="bar"
    />
  </div>
</template>

<script>
import echarts from 'echarts'
export default {
  data () {
    return {
    }
  },
  mounted () {
    // Reference from: https://echarts.apache.org/examples/zh/editor.html?c=custom-profile
    const bar = this.$refs.bar
    const echartsObj = echarts.init(bar)
    // console.log(echarts)
    const data = []
    const dataCount = 10
    const startTime = +new Date()
    const categories = ['categoryA', 'categoryB', 'categoryC']
    const types = [
      { name: 'JS Heap', color: '#7b9ce1' },
      { name: 'Documents', color: '#bd6d6c' },
      { name: 'Nodes', color: '#75d874' },
      { name: 'Listeners', color: '#e0bc78' },
      { name: 'GPU Memory', color: '#dc77dc' },
      { name: 'GPU', color: '#72b362' }
    ]

    // Generate mock data
    echarts.util.each(categories, function (category, index) {
      let baseTime = startTime
      for (let i = 0; i < dataCount; i++) {
        const typeItem = types[Math.round(Math.random() * (types.length - 1))]
        const duration = Math.round(Math.random() * 10000)
        data.push({
          name: typeItem.name,
          value: [
            index,
            baseTime,
            baseTime += duration,
            duration
          ],
          itemStyle: {
            normal: {
              color: typeItem.color
            }
          }
        })
        baseTime += Math.round(Math.random() * 2000)
      }
    })
    console.log(data)
    // https://echarts.baidu.com/option.html#series-custom.renderItem
    function renderItem (params, api) {
      const categoryIndex = api.value(0)
      const start = api.coord([api.value(1), categoryIndex])
      const end = api.coord([api.value(2), categoryIndex])
      const height = api.size([0, 1])[1] * 0.6

      const rectShape = echarts.graphic.clipRectByRect(
        {
          x: start[0],
          y: start[1] - height / 2,
          width: end[0] - start[0],
          height: height
        },
        {
          x: params.coordSys.x,
          y: params.coordSys.y,
          width: params.coordSys.width,
          height: params.coordSys.height
        })

      return rectShape && {
        type: 'rect',
        shape: rectShape,
        style: api.style()
      }
    }

    echartsObj.setOption({
      tooltip: {
        formatter: function (params) {
          return params.marker + params.name + ': ' + params.value[3] + ' ms'
        }
      },
      title: {
        text: 'Profile',
        left: 'center'
      },
      dataZoom: [{
        type: 'slider',
        filterMode: 'weakFilter',
        showDataShadow: false,
        top: 400,
        height: 10,
        borderColor: 'transparent',
        backgroundColor: '#e2e2e2',
        handleIcon: 'M10.7,11.9H9.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4h1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7v-1.2h6.6z M13.3,22H6.7v-1.2h6.6z M13.3,19.6H6.7v-1.2h6.6z', // jshint ignore:line
        handleSize: 20,
        handleStyle: {
          shadowBlur: 6,
          shadowOffsetX: 1,
          shadowOffsetY: 2,
          shadowColor: '#aaa'
        },
        labelFormatter: ''
      }, {
        type: 'inside',
        filterMode: 'weakFilter'
      }],
      grid: {
        height: 300
      },
      xAxis: {
        min: startTime,
        scale: true,
        axisLabel: {
          formatter: function (val) {
            return Math.max(0, val - startTime) + ' ms'
          }
        }
      },
      yAxis: {
        data: categories
      },
      series: [{
        type: 'custom',
        renderItem: renderItem,
        itemStyle: {
          normal: {
            opacity: 0.8
          }
        },
        encode: {
          x: [1, 2],
          y: 0
        },
        data: data
      }]
    })
  },
  methods: {
  }
}
</script>

<style scoped>
.app-container {
  box-sizing: border-box;
  min-height: calc(100vh - 50px);
}

.bar {
  width: 100%;
  height: 600px;
}
</style>

