<template>
  <!-- row 行 col 列  el-->
  <div class="mod-home">
    <el-row :gutter="20">

      <el-col :span="6">
        <div class="grid-content bg-purple clear">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="admin"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">未签到</div>
            <div class="info-num">18560</div>
          </div>
        </div>
      </el-col>

      <el-col :span="6">
        <div class="grid-content bg-purple clear">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="admin"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">异常人数</div>
            <div class="info-num">18560</div>
          </div>
        </div>
      </el-col>

      <el-col :span="6">
        <div class="grid-content bg-purple clear">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="admin"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">外出务工</div>
            <div class="info-num">18560</div>
          </div>
        </div>
      </el-col>

      <el-col :span="6">
        <div class="grid-content bg-purple clear">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="admin"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">外地人数</div>
            <div class="info-num">18560</div>
          </div>
        </div>
      </el-col>
    </el-row>

    <el-row>
      <el-col :span="24">
        <el-card>
          <div id="J_chartLineBox" class="chart-box"></div>
        </el-card>
      </el-col>
      <el-col :span="24">
        <el-card>
          <div id="J_chartBarBox" class="chart-box"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-row>
      <el-table style="width: 100%">
        <el-table-column prop="date" label="日期" width="180">
        </el-table-column>
        <el-table-column prop="name" label="姓名" width="180">
        </el-table-column>
        <el-table-column prop="address" label="地址">
        </el-table-column>
      </el-table>
    </el-row>
  </div>
</template>


<script>
  import echarts from 'echarts'
  export default {
    data() {
      return {
        chartLine: null,
        chartBar: null,
        tableData:nul
      }
    },
    mounted() {
      this.initChartLine()
      this.initChartBar()
      this.tableData=this.inittableData()
    },
    activated() {
      // 由于给echart添加了resize事件, 在组件激活时需要重新resize绘画一次, 否则出现空白bug
      if (this.chartLine) {
        this.chartLine.resize()
      }
      if (this.chartBar) {
        this.chartBar.resize()
      }
    },
    methods: {
      // 折线图
      initChartLine() {
        var option = {
          'title': {
            'text': '折线图堆叠'
          },
          'tooltip': {
            'trigger': 'axis'
          },
          'legend': {
            'data': ['邮件营销', '联盟广告', '视频广告', '直接访问', '搜索引擎']
          },
          'grid': {
            'left': '3%',
            'right': '4%',
            'bottom': '3%',
            'containLabel': true
          },
          'toolbox': {
            'feature': {
              'saveAsImage': {}
            }
          },
          'xAxis': {
            'type': 'category',
            'boundaryGap': false,
            'data': ['周一', '周二', '周三', '周四', '周五', '周六', '周日']
          },
          'yAxis': {
            'type': 'value'
          },
          'series': [{
              'name': '邮件营销',
              'type': 'line',
              'stack': '总量',
              'data': [120, 132, 101, 134, 90, 230, 210]
            },
            {
              'name': '联盟广告',
              'type': 'line',
              'stack': '总量',
              'data': [220, 182, 191, 234, 290, 330, 310]
            },
            {
              'name': '视频广告',
              'type': 'line',
              'stack': '总量',
              'data': [150, 232, 201, 154, 190, 330, 410]
            },
            {
              'name': '直接访问',
              'type': 'line',
              'stack': '总量',
              'data': [320, 332, 301, 334, 390, 330, 320]
            },
            {
              'name': '搜索引擎',
              'type': 'line',
              'stack': '总量',
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            }
          ]
        }
        this.chartLine = echarts.init(document.getElementById('J_chartLineBox'))
        this.chartLine.setOption(option)
        window.addEventListener('resize', () => {
          this.chartLine.resize()
        })
      },
      // 柱状图
      initChartBar() {
        var option = {
          tooltip: {
            trigger: 'axis',
            axisPointer: {
              type: 'shadow'
            }
          },
          legend: {
            data: ['直接访问', '邮件营销', '联盟广告', '视频广告', '搜索引擎', '百度', '谷歌', '必应', '其他']
          },
          grid: {
            left: '3%',
            right: '4%',
            bottom: '3%',
            containLabel: true
          },
          xAxis: [{
            type: 'category',
            data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日']
          }],
          yAxis: [{
            type: 'value'
          }],
          series: [{
              name: '直接访问',
              type: 'bar',
              data: [320, 332, 301, 334, 390, 330, 320]
            },
            {
              name: '邮件营销',
              type: 'bar',
              stack: '广告',
              data: [120, 132, 101, 134, 90, 230, 210]
            },
            {
              name: '联盟广告',
              type: 'bar',
              stack: '广告',
              data: [220, 182, 191, 234, 290, 330, 310]
            },
            {
              name: '视频广告',
              type: 'bar',
              stack: '广告',
              data: [150, 232, 201, 154, 190, 330, 410]
            },
            {
              name: '搜索引擎',
              type: 'bar',
              data: [862, 1018, 964, 1026, 1679, 1600, 1570],
              markLine: {
                lineStyle: {
                  normal: {
                    type: 'dashed'
                  }
                },
                data: [
                  [{
                    type: 'min'
                  }, {
                    type: 'max'
                  }]
                ]
              }
            },
            {
              name: '百度',
              type: 'bar',
              barWidth: 5,
              stack: '搜索引擎',
              data: [620, 732, 701, 734, 1090, 1130, 1120]
            },
            {
              name: '谷歌',
              type: 'bar',
              stack: '搜索引擎',
              data: [120, 132, 101, 134, 290, 230, 220]
            },
            {
              name: '必应',
              type: 'bar',
              stack: '搜索引擎',
              data: [60, 72, 71, 74, 190, 130, 110]
            },
            {
              name: '其他',
              type: 'bar',
              stack: '搜索引擎',
              data: [62, 82, 91, 84, 109, 110, 120]
            }
          ]
        }
        this.chartBar = echarts.init(document.getElementById('J_chartBarBox'))
        this.chartBar.setOption(option)
        window.addEventListener('resize', () => {
          this.chartBar.resize()
        })
      },
      /* 表格渲染*/
      inittableData() {
        return {
          tableData: [{
            date: '2016-05-02',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1518 弄'
          }, {
            date: '2016-05-04',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1517 弄'
          }, {
            date: '2016-05-01',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1519 弄'
          }, {
            date: '2016-05-03',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1516 弄'
          }]
        }
      }
    }
  }
</script>

<style>
  /* 清除浮动*/
  .clear:after {
    content: ".";
    display: block;
    height: 0;
    clear: both;
    visibility: hidden;
  }

  .mod-home {
    line-height: 1.5;
  }

  .el-row {
    margin-bottom: 20px;
  }

  .el-col {
    border-radius: 4px;
    cursor: pointer;
  }


  .bg-purple {
    background: #f0f2f5;
  }

  .grid-content {
    padding: 10px;
    border-radius: 4px;
  }

  .content-child {
    border-radius: 4px;
    text-align: center;
    float: left;
    height: 108px;
    width: 108px;
  }

  .content-info {
    text-align: center;
    margin: 26px;
    float: right;
  }

  .grid-content:hover .content-child {
    background-color: #40c9c6;
  }

  .grid-content:hover .child-icon {
    color: #fff;
  }

  .child-icon {
    margin-top: 26px;
    color: #40c9c6;
    width: 48px;
    height: 48px;
  }

  .info-text {
    margin-bottom: 12px;
    height: 18px;
    line-height: 18px;
    font-size: 16px;
    color: rgba(0, 0, 0, .45);
  }

  .info-num {
    font-size: 20px;
    color: #000;
  }

  .grid-echarts {
    background-color: #00B7EE;
    padding: 15px;
  }

  .el-alert {
    margin-bottom: 10px;
  }

  >.el-row {
    margin-top: -10px;
    margin-bottom: -10px;

    .el-col {
      padding-top: 10px;
      padding-bottom: 10px;
    }
  }

  .chart-box {
    min-height: 400px;
  }
</style>
