<template>
  <div class="background">
    <div>
      <el-row>
        <el-col :span="24">
          <div class="centTitle">张网作业方式统计分析</div>
          <div class="rightleftIcon">
            <span v-on:click="$router.back(-1)">
              <img src="../../assets/rebackLastIcon.png" style="cursor:pointer" alt="返回">
            </span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
            <router-link to="/welcome">
              <img src="../../assets/rebackMainIcon.png">
            </router-link>
          </div>
        </el-col>
      </el-row>

      <el-row>
        <!-- <el-col :span="24">
          <div class="mainEchartsStyle" id="gillEcharts">nihao</div>
        </el-col>-->
        <div class="mainEchartsStyle" id="seineEchartsId"></div>
      </el-row>
    </div>
  </div>
</template>
<script>
//seine（围网）fishing vessel statistic and analysis
export default {
  data() {
    return {
      seineOption: {
        color: ["#f44"],
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "shadow"
          }
        },
        xAxis: [
          {
            type: "category",
            data: [
              "1月",
              "2月",
              "3月",
              "4月",
              "5月",
              "6月",
              "7月",
              "8月",
              "9月",
              "10月",
              "11月",
              "12月"
            ],
            axisTick: {
              alignWithLabel: true
            },
            axisLine: {
              lineStyle: {
                color: "#5bbdff"
              }
            },
            axisLabel: {
              fontSize: 25,
              color: "#5b9bd5"
            }
          }
        ],
        yAxis: [
          {
            type: "value",
            axisLine: {
              lineStyle: {
                color: "#5bbdff"
              }
            },
            scale: true,
            axisLabel: {
              margin: 5,
              fontSize: 25,
              color: "#5b9bd5"
            }
          }
        ],
        series: [
          {
            name: "每月数量",
            type: "line",
            smooth: true,
            // data: [995, 566, 744, 348, 554, 736, 245, 446, 746, 425, 547, 356],
            data: [],
            lineStyle: {
              color: {
                type: "radial",
                x: 0.5,
                y: 0.5,
                r: 0.7,
                colorStops: [
                  {
                    offset: 0,
                    color: "red" // 0% 处的颜色
                  },
                  {
                    offset: 1,
                    color: "blue" // 100% 处的颜色
                  }
                ],
                global: false // 缺省为 false
              }
            },
            areaStyle: {
              color: {
                type: "linear",
                x: 0,
                y: 0,
                x2: 0,
                y2: 1,
                colorStops: [
                  {
                    offset: 0,
                    color: "red" // 0% 处的颜色
                  },
                  {
                    offset: 1,
                    color: "blue" // 100% 处的颜色
                  }
                ],
                global: false // 缺省为 false
              }
            }
          }
        ]
      }
    };
  },
  mounted() {
    this.drawSeineCharts();
  },
  methods: {
    reback() {
      this.$router.go(-1); //reback the last step
    },
    drawSeineCharts() {
      var seineEChart = this.$echarts.init(
        document.getElementById("seineEchartsId")
      );
      seineEChart.setOption(this.seineOption);
        //数据没有加载出来显示加载动画,样式添加todo
        seineEChart.showLoading();
        //获取数据
        this.axios.get('/getZhangwangStatistic').then(res => {
            seineEChart.hideLoading(); //加载出来隐藏加载动画
            seineEChart.setOption({  //数据添加
                series: [{
                    data: res.data.zhangwang
                }]
            })

        })
      window.addEventListener("resize", function() {
        seineEChart.resize();
      });
    }
  }
};
</script>
<style scoped>
h1,
h2,
h3,
h4,
h5,
h6,
ul,
ol,
li,
a,
input,
button,
select,
span,
td,
table {
  margin: 0;
  font-weight: normal;
  padding: 0;
  list-style: none;
}

/*the whole web background style*/
.background {
  background-image: url("../../assets/bg.png");
  background-size: 100% 100%;
  height: 1080px;
  position: absolute;
  width: 1920px;
  background-repeat: no-repeat;
}
/*the center title sytle*/
.centTitle {
  width: 536px;
  height: 43px;
  font-family: FZDHTJW--GB1-0;
  font-size: 45px;
  font-weight: normal;
  font-stretch: normal;
  letter-spacing: 5px;
  color: #58a0ee;
  margin-top: 25px;
  margin-left: 690px;
  float: left;
}
.rightleftIcon {
  margin-left: 520px;
  margin-top: 51px;
  float: left;
}
.mainEchartsStyle {
  margin-top: 141px;
  margin-left: 345px;
  height: 585px;
  width: 65%;
  float: left;
  /*background-color: #e5e9f2;*/
}
.el-row {
  margin-bottom: 20px;
  &:last-child {
    margin-bottom: 0;
  }
}

.el-col {
  border-radius: 4px;
}
.bg-purple-dark {
  background: #99a9bf;
}
.bg-purple {
  background: #d3dce6;
}
.bg-purple-light {
  background: #e5e9f2;
}
.grid-content {
  border-radius: 4px;
  min-height: 36px;
}
.row-bg {
  padding: 10px 0;
  background-color: #0732f1;
}
</style>
