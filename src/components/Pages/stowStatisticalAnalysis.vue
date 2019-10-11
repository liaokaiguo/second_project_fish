<template>
  <!-- 张网作业方式统计分析 stow net statistic and analysis -->
  <div>
    <!-- 背景 -->
    <div id="backGround"></div>
    <!-- 主要内容 -->
    <div id="main-content">
      <!-- 主标题 -->
      <div class="mainTitle">
        <p class="title" @click="refreshPage" v-bind:title="tipMsg.thisPageName">张网作业方式统计分析</p>
      </div>
      <!-- 右上角导航按钮 -->
      <div class="navigaIcon" id="navigaIcon">
        <img class="goBack" src="../../assets/backico.png" alt="后退" v-on:click="goBack"
             v-bind:title="tipMsg.goBack">
        <img class="goHome" src="../../assets/homeico.png" alt="主页" v-on:click="goHome"
             v-bind:title="tipMsg.goHome">
      </div>
      <!-- 菜单栏 -->
      <div class="setMenu">
        <!-- 选择时间菜单 -->
        <div class="selectDT">
          <form method="post">
            日期：
            <input type="date" v-model="startDate" v-bind:title="tipMsg.startDateTips"/>
            &nbsp;到&nbsp;
            <input type="date" v-model="endDate" v-bind:title="tipMsg.endDateTips"/>
          </form>
        </div>
        <!-- 菜单按钮 -->
        <div class="menuButtons">
          <div class="button">
            <button @click="search" v-bind:title="tipMsg.checkTips">查询</button>
            <button @click="save" v-bind:title="tipMsg.saveTips">保存</button>
            <button @click="reset" v-bind:title="tipMsg.resetTips">重置</button>
          </div>
        </div>
      </div>

      <!-- 左侧透明导航栏 -->
      <div id="leftNavigaList" >
        <ul>
          <li class="leftNavTitle">目&nbsp;&nbsp;&nbsp;&nbsp;录</li>
          <li><router-link to="/mapShow">地图显示</router-link></li>
          <li><router-link to="/passPort" >渔船出入港</router-link></li>
          <li><router-link to="/workModeSta" >渔船作业方式<br>统计及查询</router-link></li>
          <li><router-link to="#" >船舶明细</router-link></li>
          <li><router-link to="/purseSeineAnalysis" >围网作业方式<br>统计及分析</router-link></li>
          <li><router-link to="/trawlSA" >拖网作业方式<br>统计及分析</router-link></li>
          <li><router-link to="/gillNetStAnalysis" >刺网作业方式<br>统计及分析</router-link></li>
          <li><router-link to="/stowSA" >张网作业方式<br>统计及分析</router-link></li>
        </ul>
      </div>

      <!--主要内容展示区域-->
      <div id="showResult">
        <div id="normalBar">
          <div class="GraphTitle">正常作业统计柱状图</div>
          <div class="GraphEcharts" id="normalBarEchartsId"></div>
        </div>
        <div id="illegalOSBroken">
          <div class="GraphTitle">非法作业统计折线图</div>
          <div class="GraphEcharts" id="BrokenEchartsId"></div>
        </div>
        <div id="illegalOSPercentage">
          <div class="GraphTitle">非法作业统计占比图</div>
          <div class="GraphEcharts" id="PerIOSEchartsId"></div>
        </div>
        <div id="normalShipRadar">
          <div class="GraphTitle">正常作业渔船类型分布图</div>
          <div class="GraphEcharts" id="normalSREchartsId"></div>
        </div>
        <div id="illegalShipRadar">
          <div class="GraphTitle">非法作业渔船类型分布图</div>
          <div class="GraphEcharts" id="illegalSREchartsId"></div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
	//张网 stow net statistic and analysis
	export default {
		data() {
			return {
				// Tips提示
				tipMsg:{
					thisPageName: '张网作业方式统计分析',
					goBack: '后退',
					goHome: '主页',
					startDateTips: '请选择起始日期',
					endDateTips: '请选择终止日期',
					checkTips: '查询结果',
					resetTips: '重置条件',
					saveTips: '保存结果',
				},
				// 默认时间设置
				startDate: '2019-07-15',
				endDate: '2019-08-31',
				//正常作业柱状图选项
				NormalBarOption:{
					grid:{
						left: "13%",
						top: "10%",
						width: "75%",
						height: "78%",
					},//使得图表覆盖整个div
					tooltip: {
						trigger: "axis",
						axisPointer: {
							type: "line",
							lineStyle: {
								type: "dashed",
							},
						},
					},
					xAxis: [
						{
							type: "category",
							data: [],
							axisTick: {
								show: false,
								inside: false,
								alignWithLabel: true
							},
							axisLine: {
								lineStyle: {
									color: "#5bb1ff",
									width: 2,
								}
							},
							axisLabel: {
								fontSize: 16,
								color: "#5bb1ff",
								//rotate: 10,
								//margin:18,
								align:'center',
								fontFamily:"Times New Roman"
							}
						}
					],
					yAxis: [
						{
							minInterval: 1,
							type: "value",
							axisTick: {
								show: true,
								inside: true,
								alignWithLabel: true,
							},
							axisLine: {
								lineStyle: {
									color: "#5bb1ff",
									width: 2,
								},
							},
							scale: false,
							axisLabel: {
								fontSize: 16,
								color: "#5bb1ff",
								fontFamily:"Times New Roman"
							},
							splitLine: {
								show: true,
								lineStyle: {
									color: "#ccc",
									width: 1,
									type: "dashed",
								},
							},
						}
					],
					series:[
						{
							name:"每日数量",
							type:"bar",
							data:[],
							itemStyle:{
								barBorderRadius:[4,4,0,0],
							}
						}
					],
					color:"rgb(145,249,184)",
				},
				//非法作业折线图选项
				BrokenOption: {
					color: ["#f44"],
					grid:{
						left: "13%",
						top: "10%",
						width: "75%",
						height: "78%",
					},//使得图表覆盖整个div
					tooltip: {
						trigger: "axis",
						axisPointer: {
							type: "line",
							lineStyle: {
								type: "dashed",
							},
						},
					},
					xAxis: [
						{
							type: "category",
							data: [],
							axisTick: {
								show: false,
								inside: true,
								alignWithLabel: true
							},
							axisLine: {
								lineStyle: {
									color: "#5bb1ff",
									width: 2,
								}
							},
							axisLabel: {
								fontSize: 16,
								color: "#5bb1ff",
								//rotate: 10,
								//margin:18,
								align:'center',
								fontFamily:"Times New Roman"
							}
						}
					],
					yAxis: [
						{
							minInterval: 1,
							type: "value",
							axisTick: {
								show: true,
								inside: true,
								alignWithLabel: true,
							},
							axisLine: {
								lineStyle: {
									color: "#5bb1ff",
									width: 2,
								},
							},
							scale: false,
							axisLabel: {
								fontSize: 16,
								color: "#5bb1ff",
								fontFamily:"Times New Roman"
							},
							splitLine: {
								show: true,
								lineStyle: {
									color: "#ccc",
									width: 1,
									type: "dashed",
								},
							},
						}
					],
					series: [
						{
							name: "每日数量",
							type: "line",
							symbolSize: 2,
							showSymbol: false,
							cursor: 'pointer',
							step: false,
							lineStyle: {
								color: "#ed7d31",
								width: 2,
							},
							data: [],
						}
					]
				},
				//非法作业占比图选项
				PercentageOption: {
					grid: {
						left: "13%",
						top: "10%",
						width: "75%",
						height: "70%",
					},
					tooltip: {
						trigger: "item",
						formatter: "{a} <br/>{b} : {c} ({d}%)"
					},
					legend: {
						orient: "vertical",
						right: "8%",
						top: "middle",
						data: ["正常作业", "非法作业"],
						textStyle: {
							color: "default",
							fontSize:"100%",
						}
					},
					series: [
						{
							name: "占比情况",
							type: "pie",
							radius: "75%",
							center: ["35%", "50%"],
							labelLine: {
								show: false,
							},
							label:{
								show: false,
							},
							data: [],
							/*data: [
								{ value: 890, name: "非法作业" },
								{ value: 123, name: "正常作业" },
							],*/
							itemStyle: {
								emphasis: {
									shadowBlur: 10,
									shadowOffsetX: 0,
									shadowColor: "rgba(0, 0, 0, 0.5)"
								}
							}
						}
					],
					color: ["rgb(145,249,184)", "#ed7d31",]
				},
				//正常作业渔船类型星状图选项
				NormalShipOption:{
					tooltip:{
						show:true,
					},
					radar:
						{
							center:["45%","51%"],
							radius:"72%",
							name:{
								color:"#5bb1ff",
								fontStyle:'normal',
								fontSize:14,
							},
							nameGap:10,
							shape:"circle",
							axisLine:{
								show:true,
								lineStyle:{
									color:"#5bb1ff",
									width:1,
									opacity:1,
									type:"dashed",
								}
							},
							splitLine:{
								lineStyle:{
									color:"#CCC",
									opacity:0.6,
								}
							},
							splitArea:{
								show:false,
							},
							indicator:[
								{name:"养殖船", min:0},
								{name:"国内捕捞船",min:0},
								{name:"捕捞辅助船",min:0},
								{name:"其他辅助船",min:0},
								{name:"专业远洋渔船",min:0},
								{name:"非专业远洋渔船",min:0}
							]
						},
					series:[
						{
							name:"正常作业",
							type:"radar",
							symbol:"none",
							lineStyle:{
								color:"rgb(145,249,184)",
								width:2,
								opacity:1,
							},
							areaStyle:{
								color:'rgb(145,249,184)',
								opacity:0.3,
							},
							data:[],
						}
					]
				},
				//非法作业渔船类型星状图选项
				IllegalShipOption:{
					tooltip:{},
					radar:
						{
							center:["45%","51%"],
							radius:"72%",
							name:{
								color:"#5bb1ff",
								fontStyle:'normal',
								fontSize:14,
							},
							nameGap:10,
							shape:"circle",
							axisLine:{
								show:true,
								lineStyle:{
									color:"#5bb1ff",
									width:1,
									opacity:1,
									type:"dashed",
								}
							},
							splitLine:{
								lineStyle:{
									color:"#CCC",
									opacity:0.6,
								}
							},
							splitArea:{
								show:false,
							},
							indicator:[
								{name:"养殖船",min:0},
								{name:"国内捕捞船",min:0},
								{name:"捕捞辅助船",min:0},
								{name:"其他辅助船",min:0},
								{name:"专业远洋渔船",min:0},
								{name:"非专业远洋渔船",min:0}
							]
						},
					series:[
						{
							name:"非法作业",
							type:"radar",
							symbol:"none",
							lineStyle:{
								color:"#ed7d31",
								width:2,
								opacity:1,
							},
							areaStyle:{
								color:'#ed7d31',
								opacity:0.3,
							},
							data:[],
						}
					]
				},

				// 存储数据长度(天数)
				dataLength: 0,
				//存储占比图数据
				data: {
					normal: {value: 0, name: '正常作业'},
					illegal: {value: 0, name: '非法作业'},
				},
				shipType:['养殖船','国内捕捞船','捕捞辅助船','其他辅助船','专业远洋渔船','非专业远洋渔船'],
				/*存储返回的数据以及时间序列*/
				saveData:{},
				/*存储渔船星状图数据*/
				radarData:[],
			};
		},


		mounted() {
			this.initCharts();
		},

		methods: {
			refreshPage() {
				this.$router.go(0);
			},

			goBack() {
				this.$router.back(-1);
			},

			goHome() {
				this.$router.push('/welcome')
			},

			//初始化图表
			initCharts(){
				var myDate = new Date();
				console.log("----------" + myDate.toLocaleString() + "----------");
				console.log("正在执行init()...");
				//初始化图表
				this.dataAskDeal();
			},
			//画Echarts图
			drawCharts() {
				var _this=this;
				var brokenEChart = this.$echarts.init(
					document.getElementById("BrokenEchartsId")
				);
				var percentageEChart = this.$echarts.init(
					document.getElementById("PerIOSEchartsId")
				);
				var normalBarEcharts = this.$echarts.init(
					document.getElementById("normalBarEchartsId")
				);
				var normalSEcharts = this.$echarts.init(
					document.getElementById("normalSREchartsId")
				);
				var illegalSEcharts = this.$echarts.init(
					document.getElementById("illegalSREchartsId")
				);
				illegalSEcharts.setOption(this.IllegalShipOption);
				illegalSEcharts.setOption({
					series:[{
						data:[this.radarData.illegal],
					}]
				});
				normalSEcharts.setOption(this.NormalShipOption);
				normalSEcharts.setOption({
					series:[{
						data:[this.radarData.normal],
					}]
				});
				normalBarEcharts.setOption(this.NormalBarOption);
				console.log(this.saveData);
				normalBarEcharts.setOption({
					xAxis:[{
						data:this.saveData.time,
						axisLabel: {
							interval: function (idx, val) {
								if (idx == 0 || idx == Math.floor((_this.saveData.time.length - 1) / 2) || idx == _this.saveData.time.length  - 1) {
									return true;
								}
							},
						},
					}],
					series:[{
						data:this.saveData.normal,
					}]
				});
				brokenEChart.setOption(this.BrokenOption);
				brokenEChart.setOption({
					xAxis:[{
						data:this.saveData.time,
						axisLabel: {
							interval: function (idx, val) {
								if (idx == 0 || idx == Math.floor((_this.saveData.time.length - 1) / 2) || idx == _this.saveData.time.length  - 1) {
									return true;
								}
							},
						},
					}],
					series:[{
						data:this.saveData.illegal,
					}]
				});
				percentageEChart.setOption(this.PercentageOption);
				percentageEChart.setOption({
					series:[{
						data:[this.data.normal,this.data.illegal],
					}]
				});
				//percentageEChart.showLoading();
				window.addEventListener("resize", function() {
					brokenEChart.resize();
				});
				window.addEventListener("resize", function() {
					percentageEChart.resize();
				});
				window.addEventListener("resize", function() {
					normalBarEcharts.resize();
				});
				window.addEventListener("resize", function() {
					normalSEcharts.resize();
				});
				window.addEventListener("resize", function() {
					illegalSEcharts.resize();
				});
			},

			//数据请求及返回数据处理
			dataAskDeal () {
				this.saveData.time=[];
				var i=0; //i作为time数组的索引
				var startTime = this.getDate(this.startDate);
				var endTime = this.getDate(this.endDate);
				while((endTime.getTime()-startTime.getTime())>=0) {
					var year = startTime.getFullYear();
					var x = startTime.getMonth() + 1;//JS中的月份是0-11
					var month = x.toString().length == 1 ? "0" + x.toString() : x;

					var day = startTime.getDate().toString().length == 1 ? "0" + startTime.getDate() : startTime.getDate();
					//保存时间序列
					this.saveData.time[i]=year + '-' + month + '-' + day;
					i++;
					startTime.setDate(startTime.getDate() + 1);
				}
				console.log("开始提取数据/getDataByMonthOrDay");
				this.axios({
					method: "post",
					url: "/getDataByMonthOrDay",
					data: {
						jobType :'张网',
						startTime : this.startDate + ' 00:00:00',
						endTime : this.endDate + ' 23:59:59',
						byDay:1,
					}
				}).then((response)=>{
					var sumlegal=0;
					var sumillegal=0;
					for(var j=0;j<response.data.normal.length;j++){
						sumlegal += response.data.normal[j];
					};
					for(var j=0;j<response.data.illegal.length;j++){
						sumillegal += response.data.illegal[j];
					};
					this.saveData.normal = (response).data.normal;
					this.saveData.illegal = (response).data.illegal;
					this.data.normal.value=sumlegal;
					this.data.illegal.value=sumillegal;
					console.log("开始提取数据/countBstype");
					this.axios({
						method:"post",
						url:"/countBstype",
						data:{
							startTime : this.startDate + ' 00:00:00',
							endTime : this.endDate + ' 23:59:59',
							jobType :"张网",
						}
					}).then((res)=>{
						var normalNum=[];
						var illegalNum=[];
						for(var j=0;j<6;j++){
							normalNum[j]=(res).data.normal[this.shipType[j]];
							illegalNum[j]=(res).data.illegal[this.shipType[j]];
						}
						this.radarData.normal=normalNum;
						this.radarData.illegal=illegalNum;
						this.drawCharts()
					}).catch((response)=>{
						console.log(response);
					});
				}).catch((response)=>{
					console.log(response);
				});
			},

			//String to Date 时间调整函数 月份-1，适配Date类型
			getDate(datestr) {
				var temp = datestr.split("-");
				var date = new Date(temp[0], temp[1] - 1, temp[2]);
				return date;
			},
			// 查询方法
			search() {
				var startTime = this.getDate(this.startDate);
				var endTime = this.getDate(this.endDate);
				if (endTime.getTime() - startTime.getTime() < 0) {
					alert("终止时间不得早于起始时间！");
				} else {
					this.dataAskDeal();
				}
			},
			//保存报告
		save() {
			this.axios({
				method: "post",
				url: "/downloadExcel",
				data: {
					jobType :'张网',
					startTime : this.startDate + ' 00:00:00',
					endTime : this.endDate + ' 23:59:59',
					byDay:1
				},
				responseType:'blob'
			}).then((response)=>{
				console.log(response);
				const link = document.createElement('a');
				let blob = new Blob([response.data],{type:'application/vnd.ms-excel'});
				link.style.display = 'none';
				link.href = URL.createObjectURL(blob);
				link.setAttribute('download','张网分析报告('+this.startDate+'至'+this.endDate+').xlsx');
				document.body.appendChild(link);
				link.click();
				document.body.removeChild(link);
			}).catch((response)=> {
				console.log(response);
			});
		},
			//重置方法 恢复到默认渔场、默认时间
			reset() {
				this.startDate = '2019-07-15';
				this.endDate = '2019-08-31';
				this.dataAskDeal();
			},
		},

	};
</script>


<style scoped>
  /* #1 底层背景样式，窗口自适应 */
  /* 底层背景 */
  #backGround {
    background-image: url("../../assets/background_high.png");
    background-size: 100% 100%;
    width: 100%;
    height: 100%;
    position: absolute;
    background-repeat: no-repeat;
    /* 底层背景层 */
    z-index: 1;
    overflow: hidden;
  }
  #backGround_backup1 {
    position: absolute;
    width: 100%;
    height: 100%;
    overflow: hidden;
    /* 底层背景层 */
    z-index: 1;
  }
  /* 底层背景图片 */
  #backGround_backup1 .bgimg {
    width: 100%;
    height: 100%;
    /* 底层背景图片层 */
    z-index: 2;
  }

  /* #2 主要内容样式，窗口自适应 */
  /* 主要内容 */
  #main-content {
    position: absolute;
    width: 100%;
    height: 100%;
    overflow: hidden;
    /* 内容层 */
    z-index: 80;
  }

  /* #2.1 中心标题样式 */
  .mainTitle {
    position: absolute;
    left: 35vw;
    width: 30vw;
    top: 0;
    height: 5.4vh;
    text-align:center;
    /* background-color 测试用 */
    /*background-color: #FFFFFF;*/
  }
  .mainTitle .title {
    font-family: Microsoft YaHei;
    color: #5bb1ff;
    cursor: pointer;
    letter-spacing: 0.2vw;
    font-size: 4.3vh;
    display: inline-block;
    /* background-color 测试用 */
    /*background-color: #ff5234;*/
  }

  /* #2.2 右上角导航按钮 */
  #main-content .navigaIcon {
    position: absolute;
    right: 2vw;
    width: 15vw;
    height: 5.5vh;
    margin-top: 5vh;
    margin-bottom: 0.1vh;
    /* background-color 测试用 */
    /* background-color: #FFFFFF; */
  }

  #main-content .navigaIcon .goBack {
    cursor: pointer;
    position: absolute;
    right: 50%;
    height: 80%;
  }

  #main-content .navigaIcon .goHome {
    cursor: pointer;
    position: absolute;
    right: 25%;
    height: 80%;
  }

  /* #2.3 中上方菜单栏 */
  #main-content .setMenu {
    position: absolute;
    width: 80vw;
    height: 6vh;
    left: 10vw;
    top: 9.5vh;
    margin-top: 1vh;
    margin-bottom: 1vh;
    color: #5bb1ff;
    /* background-color 测试用 */
    /* background-color: #FFFFFF;*/
  }

  /* 选择日期时间菜单 */
  #main-content .setMenu .selectDT {
    position: absolute;
    left: 10%;
    width: 50%;
    height: 100%;
    font-size: 2.8vh;
    /* background-color 测试用 */
    /* background-color: #FFFFFF;*/
  }

  #main-content .setMenu .selectDT form input {
    width: 35%;
    height: 5vh;
    background-color: rgba(255, 255, 255, 0.04);
    color: #5bb1ff;
    border-color: #5bb1ff;
    text-align: center;
  }

  /*  修改日历控件类型 */
  /*控制编辑区域的*/
  input[type="date"]::-webkit-datetime-edit {
    background-color: rgba(255, 255, 255, 0);
  }

  /*控制年月日这个区域的*/
  input[type="date"]::-webkit-datetime-edit-fields-wrapper {
    /*background-color: #FFFFFF;*/
  }

  /*这是控制年月日之间的斜线或短横线的*/
  input[type="date"]::-webkit-datetime-edit-text {
    color: #5bb1ff;
    padding: 0.5em;
  }

  /*控制年文字, 如2019四个字母占据的那片地方*/
  input[type="date"]::-webkit-datetime-edit-year-field {
    color: #5bb1ff;
    background-color: none;
  }

  /*控制月文字*/
  input[type="date"]::-webkit-datetime-edit-month-field {
    color: #5bb1ff;
    background-color: none;
  }

  /*控制日文字*/
  input[type="date"]::-webkit-datetime-edit-day-field {
    color: #5bb1ff;
    background-color: none;
  }

  /*这是控制上下小箭头的*/
  input[type="date"]::-webkit-inner-spin-button {
    /* 直接隐藏 */
    visibility: hidden;
  }

  /*这是控制下拉小箭头的*/
  input[type="date"]::-webkit-calendar-picker-indicator {
    position: relative;
    right: 3%;
    border: 0.1em solid #58a0ee;
    border-radius: 0.2em;
    color: #0e0270;
    background-image: -webkit-linear-gradient(top, #58a0ee, #acc8e6);
    visibility: visible;
  }

  /*控制清除按钮*/
  input[type="date"]::-webkit-clear-button {
    /* 直接隐藏 */
    visibility: hidden;
  }
  /*菜单按钮栏*/
  #main-content .setMenu .menuButtons {
    position: absolute;
    left: 68%;
    width: 25%;
    height: 100%;
    font-size: 2.8vh;
    /* background-color 测试用 */
    /* background-color: #FFFFFF;*/
  }
  /*菜单按钮*/
  #main-content .setMenu .menuButtons button {
    width: 24%;
    height: 5vh;
    background-color: rgba(8, 17, 44, 0.11);
    color: #5bb1ff;
    border-color: #5bb1ff;
    cursor: pointer;
  }

  /* #2.4 左侧透明导航栏 */
  #leftNavigaList{
    position: absolute;
    top: 18vh;
    left: 1vw;
    width: 8vw;
    height: 72vh;
    z-index: 99;
    /*text-align: center;*/
    background-color: rgba(0, 0, 0, 0.24);
    border-radius: 0.5rem;
  }
  #leftNavigaList ul{
    /* 清除ul标签的默认样式 */
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    margin: 0;
    padding: 0;
    font-size: 2.2vh;
    list-style-type: none;
    display: block;
    /*background-color: #3f72c5;*/
  }
  #leftNavigaList .leftNavTitle{
    float: top;     /* 使li内容纵向浮动 */
    margin-top:0;   /* 两个li之间的距离 */
    display: block;
    color: #62dbff;
    text-align: center;
    padding: 1vh 1vh;
    font-size: 2.4vh;
    font-weight: bold;
    border-bottom: #55a6ee 1px solid;
  }
  #leftNavigaList li {
    float: top;     /* 使li内容纵向浮动 */
    margin-top: 0.5vh;   /* 两个li之间的距离 */
    /*border: solid 1px #191762;*/
  }
  #leftNavigaList li a {
    /* 设置链接内容显示的格式*/
    /* 把链接显示为块元素可使整个链接区域可点击 */
    display: block;
    color: #62dbff;
    text-align: center;
    padding: 1vh 1vh;
    /* 去除下划线 */
    text-decoration: none;
  }
  #leftNavigaList li a:hover{
    color: #0c034b;
    /* 鼠标选中时背景变色 */
    /*用背景色*/
    /* 浏览器不支持的时候显示 */
    /*background-color: #40c0ff;*/
    /* 标准的语法（必须放在最后） */
    background-image: radial-gradient(#96f0ff, #5ee4ff, #40c0ff);
    /*用图片*/
    /*height:100%;*/
    /*width:100%;*/
    /*overflow: hidden;*/
    /*background-size:cover;*/
    /*或者background-size:100%;*/
    /*background-image: url("../../assets/菜单选中背景.png");*/
    /*background-repeat: no-repeat;*/
  }

  /*2.5 结果显示区域格式*/
  /*磨砂背景*/
  #showResult{
    position: absolute;
    width: 85vw;
    height: 78vh;
    left: 12vw;
    top: 18vh;
    margin-top: 0vh;
    margin-bottom: 0vh;
    background-color: rgba(0, 0, 0, 0.14);
    border-radius: 2rem;
  }
  /*正常作业柱状图格式*/
  #normalBar{
    position: absolute;
    width: 32%;
    height: 48%;
    top: 2%;
    left: 1%;
    /* background-color 测试用 */
    /*background-color: #FFFFFF;*/
    background-image:url("../../assets/msgBg2.png");
    background-size: 100% 100%;
    background-repeat: no-repeat;
  }
  /*非法作业折线图模块格式*/
  #illegalOSBroken {
    position: absolute;
    width: 32%;
    height: 48%;
    top: 2%;
    left: 34%;
    /* background-color 测试用 */
    /*background-color: #FFFFFF;*/
    background-image:url("../../assets/msgBg2.png");
    background-size: 100% 100%;
    background-repeat: no-repeat;
  }
  /*非法作业占比图*/
  #illegalOSPercentage {
    position: absolute;
    width: 32%;
    height: 48%;
    top: 2%;
    left: 67%;
    /* background-color 测试用 */
    /*background-color: #FFFFFF;*/
    background-image:url("../../assets/msgBg2.png");
    background-size: 100% 100%;
    background-repeat: no-repeat;
  }
  /*正常作业渔船星状图*/
  #normalShipRadar{
    position: absolute;
    width: 36%;
    height: 48%;
    left: 12%;
    top: 52%;
    /* background-color 测试用 */
    /*background-color: #FFFFFF;*/
    background-image:url("../../assets/msgBg2.png");
    background-size: 100% 100%;
    background-repeat: no-repeat;
  }
  /*非法作业渔船星状图*/
  #illegalShipRadar{
    position: absolute;
    width: 36%;
    height: 48%;
    left: 52%;
    top: 52%;
    /* background-color 测试用 */
    /*background-color: #FFFFFF;*/
    background-image:url("../../assets/msgBg2.png");
    background-size: 100% 100%;
    background-repeat: no-repeat;
  }
  .GraphTitle{
    position: absolute;
    top: 0%;
    left: 20%;
    width: 60%;
    height: 10%;
    margin-top: 1.15%;
    font-size: 2.8vh;
    letter-spacing: 0.1vw;
    color: #5bb1ff;
    /* background-color 测试用 */
    /*background-color: #FFFFFF;*/
  }
  .GraphEcharts{
    top: 15%;
    left: 6%;
    height: 75%;
    width: 88%;
    position: absolute;
    /* background-color 测试用 */
    /*background-color: #FFFFFF;*/
  }
</style>
