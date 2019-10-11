<template>
  <div>
    <h2>vue中插入Echarts示例</h2>
    <!--<echartsu v-bind:id="'chart_example'" :data="option"></echartsu>-->
    <!--<echartsu v-bind:id="'chart_example'"></echartsu>-->
    <!--<echartsu v-bind:id="'chart_example'"></echartsu>-->
    <div>
      <testr v-bind:echartId="'chart_example'" :OptionData="option"></testr>
    </div>

    <!--<div id="chart_example2" :style="{width: '300px', height: '300px'}"></div>-->
    <div class="chartbg">
      <div id="chart_example2" class="chartstyle"></div>
    </div>
  </div>
</template>

<script>
//require("echarts/lib/chart/bar");
// 引入提示框和title组件
//require("echarts/lib/component/tooltip");
//require("echarts/lib/component/title");
import echartsu from "@/components/echartsGraph";
import testr from "@/components/common/MiddleBottomEchartsUtils";

export default {
  data() {
    return {
      option: {
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
            }
          }
        ],
        yAxis: [
          {
            type: "value"
          }
        ],
        series: [
          {
            name: "每月花费",
            type: "bar",
            barWidth: "60%",
            data: [995, 666, 444, 858, 654, 236, 645, 546, 846, 225, 547, 356]
          }
        ]
      }
    };
  },
  components: {
    echartsu,
    testr
  },
  mounted() {
    //var myChart = this.$echarts.init(document.getElementById("chart_example"));
    var myChart2 = this.$echarts.init(
      document.getElementById("chart_example2")
    );

    // myChart.setOption(option);
    myChart2.setOption(this.option);
  }
};
</script>
<style scoped>
.chartbg {
  background-image: url("../assets/msgBg.png");
}
.chartstyle {
  margin-top: 30px;
  margin-left: 50px;
  width: 300px;
  height: 300px;
}
</style>
