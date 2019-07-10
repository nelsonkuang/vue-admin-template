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
    // Reference from: https://www.cnblogs.com/xufeikko/p/9956765.html
    // 在区间段内展示连续数据
    const colors = ['#4f81bd', '#c0504d', '#9bbb59', '#604a7b']
    const bar = this.$refs.bar
    const echartsObj = echarts.init(bar)
    // console.log(echarts)
    echartsObj.setOption({
      color: colors,
      tooltip: {
        formatter: function (params) {
          return params.name + ':' + params.value[1] + '~' + params.value[2]
        }
      },
      grid: {
        left: '3%',
        right: '3%',
        top: '1%',
        bottom: '10%',
        containLabel: true
      },
      xAxis: {
        data: ['2018-06-15', '2018-06-25', '2018-07-01', '2018-08-25', '2018-11-14', '2018-12-13']
      },
      yAxis: {
        data: ['甲泼尼龙片', '醋酸泼尼松片', '缬沙坦胶囊']
      },
      series: [
        {
          type: 'custom',
          renderItem: function (params, api) {
            const categoryIndex = api.value(0)
            const start = api.coord([api.value(1), categoryIndex])
            const end = api.coord([api.value(2), categoryIndex])
            const height = 24
            // console.log(api.style())

            return {
              type: 'rect',
              shape: echarts.graphic.clipRectByRect({
                x: start[0],
                y: start[1] - height / 2,
                width: end[0] - start[0],
                height: height
              }, {
                x: params.coordSys.x,
                y: params.coordSys.y,
                width: params.coordSys.width,
                height: params.coordSys.height
              }),
              style: { ...api.style(), fill: colors[params.dataIndex] }
            }
          },
          encode: {
            x: [1, 2],
            y: 0
          },
          data:
            [
              // value[ 维度0 维度1 维度2]
              // dataItem
              {
                name: '甲泼尼龙片',
                value: [0, '2018-08-25', '2018-12-13'] // 0,1,2代表y轴的索引，后两位代表x轴数据开始和结束
              },
              // dataItem
              {
                name: '醋酸泼尼松片',
                value: [1, '2018-06-15', '2018-11-14']
              },
              // dataItem
              {
                name: '缬沙坦胶囊',
                value: [2, '2018-06-25', '2018-07-01']
              }
            ]
        }
      ]
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
  height: 500px;
}
</style>

