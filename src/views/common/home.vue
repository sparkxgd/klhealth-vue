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
            <div class="info-num">18560</div>
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
            <div class="info-num">18560</div>
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
            <div class="info-num">18560</div>
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
            <div class="info-num">18560</div>
          </div>
        </div>
      </el-col>
    </el-row>

    <el-row>
      <div id="box" :style="{height:'500px',width:'100%'}"></div>
    </el-row>
  </div>
</template>
<script>
  export default {
    data() {
      return {

      }
    },
    mounted() {
      this.EpiMapData()
    },
    activated() {

    },
    methods: {
      /* 地图数据*/
      EpiMapData() {
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
            text: '疫情地图',
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
            data: mydata
          }]
        };
        // 初始化echarts实例
        var myEcharts = echarts.init(document.getElementById("box"))
        // 使用刚指定的配置项和数据显示图表。
        myEcharts.setOption(option);
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
</style>
