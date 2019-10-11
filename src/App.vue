<template>
  <div id="app">
    <!--顶部视图-->
    <div></div>
    <router-view></router-view>
  </div>
</template>

<script>
import top from "@/components/common/TopUtils";

export default {
  name: "App",
  components: {
    top
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding: 0;
  margin: 0;
}
/* 去掉百度地图logo */
.BMap_cpyCtrl {
  display: none;
}
.anchorBL {
  display: none;
}
</style>
