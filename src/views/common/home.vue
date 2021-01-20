<template>
  <!-- row 行 col 列  el-->
  <div class="mod-home">
    <el-row :gutter="20">

      <el-col :span="6">
        <div class="grid-content bg-purple clear" @click="home_card_click(1)">
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
        <div class="grid-content bg-purple clear" @click="home_card_click(2)">
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
        <div class="grid-content bg-purple clear" @click="home_card_click(3)">
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
        <div class="grid-content bg-purple clear" @click="home_card_click(4)">
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

    <el-row :gutter="24" class="clear">

      <el-col :span="15" style="float: left;" class="row_chart">
        <el-card>
          <div class="row-title">新闻动态</div>
          <el-table :data="newdatas" @row-click="newsClick" border style="width: 100%">
            <el-table-column :show-overflow-tooltip="true" prop="title" label="标题" width="180"></el-table-column>
            <el-table-column prop="newsType" label="新闻类型" width="180">
              <template slot-scope="scope">
                <el-tag v-if="scope.row.newsType === 1" size="small" type="danger">学院新闻</el-tag>
                <el-tag v-else size="small">疫情新闻</el-tag>
              </template>
            </el-table-column>
            <el-table-column prop="releaseTime" label="发布时间" width="180"></el-table-column>
            <el-table-column :show-overflow-tooltip="true" prop="author" label="作者"></el-table-column>
            <el-table-column prop="lookNum" label="浏览量"></el-table-column>
          </el-table>
        </el-card>
      </el-col>

      <el-col :span="9" style="float: right;" class="row_chart">
        <el-card>
          <div class="row-title">公告通知</div>
          <el-table :data="TableData" border style="width: 100%">
            <el-table-column prop="date" label="日期" width="120">
            </el-table-column>
            <el-table-column :show-overflow-tooltip="true" prop="name" label="标题" width="120">
            </el-table-column>
            <el-table-column :show-overflow-tooltip="true" prop="address" label="内容">
            </el-table-column>
          </el-table>
        </el-card>
      </el-col>

      <el-col :span="24" class="row_chart">
        <el-card>
          <div class="row-title">学院签到统计</div>
          <div id="J_chartLineBox" class="chart-box"></div>
        </el-card>
      </el-col>

      <el-col :span="24" class="row_chart">
        <el-card>
          <div id="J_chartBarBox" class="chart-box"></div>
        </el-card>
      </el-col>
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
        TableData: null,
        newdatas: []
      }
    },
    mounted() {
      this.initChartLine()
      this.initChartBar()
      this.TableData = this.initTableData().tableData
      this.initNewsData()
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
            'text': ''
          },
          'tooltip': {
            'trigger': 'axis'
          },
          'legend': {
            'data': ['人文学院', '外国语学院', '教育科学院', '旅游学院', '经济与管理学院','马克思主义学院','理学院','大数据工程学院','大健康学院','建筑工程学院','音乐与舞蹈学院','美术与设计学院','体育学院','国际教育学院','继续教育学院']
          },
          'grid': {
            'left': '3%',
            'right': '4%',
            'bottom': '3%',
            'containLabel': true
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
              'name': '人文学院',
              'type': 'line',
              'stack': '总量',
              'data': [120, 132, 101, 134, 90, 230, 210]
            },
            {
              'name': '外国语学院',
              'type': 'line',
              'stack': '总量',
              'data': [220, 182, 191, 234, 290, 330, 310]
            },
            {
              'name': '教育科学院',
              'type': 'line',
              'stack': '总量',
              'data': [150, 232, 201, 154, 190, 330, 410]
            },
            {
              'name': '国际教育学院',
              'type': 'line',
              'stack': '总量',
              'data': [320, 332, 301, 334, 390, 330, 320]
            },
            {
              'name': '旅游学院',
              'type': 'line',
              'stack': '总量',
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            },
            {
              'name': '经济与管理学院',
              'type': 'line',
              'stack': '总量',
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            },
            {
              'name': '马克思主义学院',
              'type': 'line',
              'stack': '总量',
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            },
            {
              'name': '理学院',
              'type': 'line',
              'stack': '总量',
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            },
            {
              'name': '大数据工程学院',
              'type': 'line',
              'stack': '总量',
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            },
            {
              'name': '大健康学院',
              'type': 'line',
              'stack': '总量',
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            },
            {
              'name': '建筑工程学院',
              'type': 'line',
              'stack': '总量',
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            },
            {
              'name': '音乐与舞蹈学院',
              'type': 'line',
              'stack': '总量',
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            },
            {
              'name': '美术与设计学院',
              'type': 'line',
              'stack': '总量',
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            },
            {
              'name': '体育学院',
              'type': 'line',
              'stack': '总量',
              'data': [820, 932, 901, 934, 1290, 1330, 1320]
            },
            {
              'name': '继续教育学院',
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
      /* 新闻数据获取*/
      initNewsData() {
        this.$http({
          url: this.$http.adornUrl('/epi/news/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': 1,
            'limit': 4,
            'key': ''
          })
        }).then(({
          data
        }) => {
          if (data && data.code === 0) {
            this.newdatas = data.page.list
          } else {
            this.newdatas = []
          }
        })
      },
      /* 通知数据获取*/
      initTableData() {
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
      },
      home_card_click(item) {
        switch (item) {
          case 1:
            console.log('未签到')
            break;
          case 2:
            console.log('异常人数')
            break;
          case 3:
            console.log('外出务工')
            break;
          case 4:
            console.log('外地人数')
            break;
        }
      },
      newsClick(row) {
        alert(row.title)
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

  .row-title {
    color: #000000;
    font-size: 20px;
    margin: 0 0 20px 0;
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

  .row_chart {
    margin-bottom: 20px;
  }

  .chart-box {
    min-height: 400px;
  }
</style>
