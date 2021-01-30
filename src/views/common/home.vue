<template>
  <!-- row 行 col 列  el-->
  <div class="mod-home">
    <el-row :gutter="20">

      <el-col :span="6">
        <div class="grid-content bg-purple clear" @click="home_card_click(1)">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="all-people"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">系统总人数</div>
            <div id="systemNum" class="info-num">18560</div>
          </div>
        </div>
      </el-col>

      <el-col :span="6">
        <div class="grid-content bg-purple clear" @click="home_card_click(2)">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="no-sign-in"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">今日未签到</div>
            <div id="dayNoSignIn" class="info-num">18560</div>
          </div>
        </div>
      </el-col>

      <el-col :span="6">
        <div class="grid-content bg-purple clear" @click="home_card_click(3)">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="sign-in"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">今日已签到</div>
            <div id="daySignIn" class="info-num">18560</div>
          </div>
        </div>
      </el-col>

      <el-col :span="6">
        <div class="grid-content bg-purple clear" @click="home_card_click(4)">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="task"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">今日任务</div>
            <div id="dayTask" class="info-num">18560</div>
          </div>
        </div>
      </el-col>
    </el-row>
    <el-row :gutter="20">
      <el-col :span="6">
        <div class="grid-content bg-purple clear" @click="home_card_click(4)">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="nor"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">今日正常人数</div>
            <div id="dayPeoNormalNum" class="info-num">18560</div>
          </div>
        </div>
      </el-col>

      <el-col :span="6">
        <div class="grid-content bg-purple clear" @click="home_card_click(4)">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="abn"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">今日异常人数</div>
            <div id="dayPeoAbnormalNum" class="info-num">18560</div>
          </div>
        </div>
      </el-col>

      <el-col :span="6">
        <div class="grid-content bg-purple clear" @click="home_card_click(4)">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="com"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">今日任务已完成</div>
            <div id="dayTaskComplete" class="info-num">18560</div>
          </div>
        </div>
      </el-col>

      <el-col :span="6">
        <div class="grid-content bg-purple clear" @click="home_card_click(4)">
          <div class="content-child">
            <icon-svg class="child-icon" style="width: 48px; height: 48px;" name="unfinished"></icon-svg>
          </div>

          <div class="content-info">
            <div class="info-text">今日任务未完成</div>
            <div id="dayTaskUnfinished" class="info-num">18560</div>
          </div>
        </div>
      </el-col>
    </el-row>

    <el-row>
      <div id="box" :style="{height:'500px',width:'100%'}"></div>
    </el-row>

    <el-row>
      <el-col :span="24">
        <el-card>
          <div id="J_chartLineBox" class="chart-box"></div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>
<script>
  export default {
    data() {
      return {
        chartLine: null,
        epiMapData:null,
      }
    },
    mounted() {
      this.getDataInfo()
      this.initChartLine()
    },
    activated() {
      if (this.chartLine) {
        this.chartLine.resize()
      }
    },
    methods: {
      /* 获取数据*/
      getDataInfo(){
        this.dataListLoading = true;
        this.$http({
          url: this.$http.adornUrl('/epi/home/data'),
          method: 'get'
        }).then(datas=>{
          document.querySelector("#systemNum").innerText=datas.data.data.sysUserNum;
          document.querySelector("#dayNoSignIn").innerText=datas.data.data.dayNoSignIn;
          document.querySelector("#daySignIn").innerText=datas.data.data.daySignIn;
          document.querySelector("#dayTask").innerText=datas.data.data.dayTask;
          document.querySelector("#dayPeoNormalNum").innerText=datas.data.data.peoNormalNum;
          document.querySelector("#dayPeoAbnormalNum").innerText=datas.data.data.peoAbnormalNum;
          document.querySelector("#dayTaskComplete").innerText=datas.data.data.taskComplete;
          document.querySelector("#dayTaskUnfinished").innerText=datas.data.data.taskUnFinishde;
          this.epiMapData=datas.data.data.areas;
          this.EpiMapData(this.epiMapData)
          this.dataListLoading = false;
        })
      },
      /* 地图数据*/
      EpiMapData(data) {
        function randomData() {
          return Math.round(Math.random() * 500);
        }

        var mydata = [{
            name: '北京',
            value: randomData()
          }, {
            name: '天津',
            value: randomData()
          },
          {
            name: '上海',
            value: randomData()
          }, {
            name: '重庆',
            value: randomData()
          },
          {
            name: '河北',
            value: randomData()
          }, {
            name: '河南',
            value: randomData()
          },
          {
            name: '云南',
            value: randomData()
          }, {
            name: '辽宁',
            value: randomData()
          },
          {
            name: '黑龙江',
            value: randomData()
          }, {
            name: '湖南',
            value: randomData()
          },
          {
            name: '安徽',
            value: randomData()
          }, {
            name: '山东',
            value: randomData()
          },
          {
            name: '新疆',
            value: randomData()
          }, {
            name: '江苏',
            value: randomData()
          },
          {
            name: '浙江',
            value: randomData()
          }, {
            name: '江西',
            value: randomData()
          },
          {
            name: '湖北',
            value: randomData()
          }, {
            name: '广西',
            value: randomData()
          },
          {
            name: '甘肃',
            value: randomData()
          }, {
            name: '山西',
            value: randomData()
          },
          {
            name: '内蒙古',
            value: randomData()
          }, {
            name: '陕西',
            value: randomData()
          },
          {
            name: '吉林',
            value: randomData()
          }, {
            name: '福建',
            value: randomData()
          },
          {
            name: '贵州',
            value: randomData()
          }, {
            name: '广东',
            value: randomData()
          },
          {
            name: '青海',
            value: randomData()
          }, {
            name: '西藏',
            value: randomData()
          },
          {
            name: '四川',
            value: randomData()
          }, {
            name: '宁夏',
            value: randomData()
          },
          {
            name: '海南',
            value: randomData()
          }, {
            name: '台湾',
            value: randomData()
          },
          {
            name: '香港',
            value: randomData()
          }, {
            name: '澳门',
            value: randomData()
          }
        ];

        var option = {
          backgroundColor: '#FFFFFF',
          title: {
            text: '用户活动区域',
            subtext: '纯属虚构',
            x: 'center'
          },
          tooltip: {
            trigger: 'item'
          },
          visualMap: {
            show: true,
            x: 'left',
            y: 'bottom',
            splitList: [{
                start: 500,
                end: 600
              }, {
                start: 400,
                end: 500
              },
              {
                start: 300,
                end: 400
              }, {
                start: 200,
                end: 300
              },
              {
                start: 100,
                end: 200
              }, {
                start: 0,
                end: 100
              },
            ],
            color: ['#5475f5', '#9feaa5', '#85daef', '#74e2ca', '#e6ac53', '#9fb5ea']
          },
          series: [{
            name: '随机数据',
            type: 'map',
            mapType: 'china',
            roam: false,
            label: {
              normal: {
                show: false
              },
              emphasis: {
                show: false
              }
            },
            data: data
          }]
        };
        // 初始化echarts实例
        var myEcharts = echarts.init(document.getElementById("box"))
        // 使用刚指定的配置项和数据显示图表。
        myEcharts.setOption(option);
      },
      initChartLine() {
        function randomData() {
          return Math.round(Math.random() * 500);
        }
        var option = {
          'title': {
            'text': '学院签到数据分析'
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
    height: 105px;
    width: 105px;
  }

  .content-info {
    text-align: center;
    margin: 26px;
    float: right;
  }

  .grid-content:hover .content-child {
    background-color: #fff;
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

  .chart-box {
    min-height: 400px;
  }
</style>
