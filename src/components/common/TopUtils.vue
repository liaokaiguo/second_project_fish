<!--主要是标题共用组件，具有主要菜单等功能-->
<template>
  <div class="topcss">{{msg}}</div>
</template>

<script>
export default {
  data() {
    return {
      msg: "nihao"
    };
  }
};
</script>

<style>
.topcss {
  width: 100%;
  height: 10px;
  text-align: left;
  color: black;
}
</style>

