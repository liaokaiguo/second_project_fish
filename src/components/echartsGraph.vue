<template>
  <div>
    <h1>nihao</h1>
    <!-- <div v-bind:id="id" v-bind:data="data" :style="{width: '300px', height: '300px'}"></div>-->
    <!--<div v-bind:id="id" :style="{width: '300px', height: '300px'}"></div>-->
    <div v-bind:id="id" class="chartstyle"></div>
  </div>
</template>
<script>
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
      },
      Graphs: null
    };
  },
  watch: {
    data: {
      handler(newValue, oldValue) {
        this.drawGraph(this.id, newValue);
        //this.drawGraph(this.id, this.option);
      },
      deep: true
    }
  },
  //props: ["id", "data"],
  // props: ["id"],
  props: ["id", "stylet"],
  mounted: function() {
    //this.drawGraph(this.id, this.data);
    this.drawGraph(this.id, this.option);
  },
  methods: {
    drawGraph(id, data) {
      // var myChart = this.$echarts.init(document.getElementById(id));
      // myChart.setOption(data);
      this.Graphs = this.$echarts.init(document.getElementById(id));
      this.Graphs.setOption(data);
      window.addEventListener("resize", function() {
        this.Graphs.resize();
      });
    }
  },
  beforeDestroy() {
    if (this.Graphs) {
      this.Graphs.clear();
    }
  }
};
</script>
<style >
.chartstyle {
  margin-top: 30px;
  margin-left: 50px;
  width: 300px;
  height: 300px;
}
</style>