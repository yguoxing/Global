<template>
  <div class="chart-content">
    <div id='myMonthChart'>
    </div>
  </div>
</template>

<script>
import echarts from 'echarts'
// require('echarts/theme/dark');

export default {
  props: ['monthProduce'],
  data () {
    return {
      dataShadow: [] // 用于阴影柱状图显示
    }
  },
  data () {
    return {
      chart: null,
      seriesData: [],
      yAxisData: this.monthProduce.yAxis // ['新增POI100个','更新PIO3342个','新增道路23公里','更新道路342公里']
    }
  },
  methods: {
    // 获取背景柱状图数组
    shadowMax() {
      var max = 0;
      var maxArr = [];
      for (let i = 0; i < this.monthProduce.barData.length; i++) {
        let temp = parseInt(this.monthProduce.barData[i]);
        if (max < temp) {
          max = temp
        }
      }
      max = Math.ceil(max / 100) * 100;
      this.monthProduce.barData.forEach(function (item, index, arr) {
        maxArr[index] = max;
      });
      return maxArr;
    },
    // 绘制表格
    drawGraph() {
        this.dataShadow = this.shadowMax();

        if (!this.chart) {
          this.chart = echarts.init(document.getElementById('myMonthChart'))
        }

        this.chart.showLoading()
        this.chart.setOption({
            backgroundColor: 'rgba(128, 128, 128, 0)',
            grid: {
              left: 170,
              right: 70,
              top: 5,
              bottom: 15
            },
            legend: {
                itemWidth: 14,
                bottom: 0,
                left: 170,
                data:[{ name:'月均线', icon: 'roundRect', textStyle: {color: '#FFFFFF'}}]
            },
            xAxis: {
              show: false
            },
            yAxis: {
                data: this.monthProduce.yAxis,
                boundaryGap: true, // 坐标轴两边留空白
                axisLine: {
                  show:true
                },
                axisTick: {
                  show: false
                },
                axisLabel: {
                  formatter:function (value) {
                    let arr = value.split(' ');
                    return arr[0] + ' {' + 'a|' + arr[1] + '}' + arr[2];
                  },
                  fontSize: 14,
                  fontWeight: 'bold',
                  color:'#DDDDDD',
                  rich: {
                    a: {
                      color: '#FD8E20',
                      fontWeight: 'bold',
                      fontSize: 14
                    }
                  }
                }
            },
            series: [{    // For shadow
              type: 'bar',
              itemStyle: {
                  normal: {
                     color: 'rgba(255,255,255,0.15)',
                     barBorderRadius:[0, 5, 5, 0]
                  }
              },
              barGap:'-100%', // 两个柱子重叠
              barCategoryGap:'80%', // 柱子之间的间距
              data: this.dataShadow,
              animation: false
            },{
               type:'bar',
               itemStyle: {
                 normal: {
                   color: '#39f',
                   barBorderRadius:[0, 5, 5, 0],
                   label: {
                     show: false,
                     position: 'right',
                     textStyle: {
                       color: '#FD8E20'
                     }
                   }
                 }
               },
               data: this.monthProduce.barData // [100, 90, 80, 70]
             },{
               name:'月均线',
               type:'line',
               symbol:'none',  // 去掉点
               smooth: true,
               itemStyle: {
                 normal: {
                   color: '#FD8E20'
                 }
               },
               data: this.monthProduce.lineData
            }]
        })
        this.chart.hideLoading()
    }
  },
  mounted() {
    this.$nextTick(function() {
      this.drawGraph()
    })
  },
  watch: {
    monthProduce: {
      handler(curVal,oldVal){
        this.$nextTick(function() {
          this.drawGraph()
        })
　　　　},
　　　　deep:true
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.chart-content{
    color: #DDD;
    width: 400px;
    position: relative;
    display: inline-block;
}
#myMonthChart {
    width: 400px;
    height: 200px;
    margin-left: 35px;
}
.labelSpan{
    position: absolute;
    display: inline;
    right: 0px;
    top: -6px;
}
.labelSpan span{
    margin: 27px;
    display: block;
    text-align: right;
    color: #f48b2d;
    font-size: 13px;
    font-weight: 600;
}
</style>
