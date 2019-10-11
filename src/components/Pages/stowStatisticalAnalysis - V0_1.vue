<template>
	<!-- 张网作业方式统计分析 stow net statistic and analysis -->
	<div>
		<!-- 背景 -->
		<div id="main-bkground">
			<img class="img" src="../../assets/bg.png"/>
		</div>
		<!-- 主要内容 -->
		<div id="main-content">
			<!-- 主标题 -->
			<div class="centTitle">
				<p class="myp1" @click="refreshPage" v-bind:title="thisPageTips">张网作业方式统计分析</p>
			</div>

			<!-- 导航按钮 -->
			<div class="navigaIcon" id="navigaIcon">
				<img class="goBack" src="../../assets/rebackLastIcon.png" alt="后退" v-on:click="goBack"
				     v-bind:title="backTips">
				<img class="goHome" src="../../assets/rebackMainIcon.png" alt="主页" v-on:click="goHome"
				     v-bind:title="homeTips">
			</div>

			<!-- 菜单栏 -->
			<div class="setMenu">
				<!-- 选择渔场菜单 -->
				<div class="selectFG">
					<form method="post" v-bind:title="selectFGTips">
						<label>渔场：</label>
						<select v-model="fishGround">
							<option v-for="FGoption in FGoptions" v-bind:value="FGoption.value">
								{{FGoption.value}}
							</option>
						</select>
					</form>
				</div>
				<!-- 选择时间菜单 -->
				<div class="selectDT">
					<form method="post">
						<label for="date">日期：</label>
						<input type="date" v-model="startDate" v-bind:title="startDateTips"/>
						&nbsp;到&nbsp;
						<input type="date" v-model="endDate" v-bind:title="endDateTips"/>
					</form>
				</div>
				<!-- 菜单按钮 -->
				<div class="menuButtons">
					<div class="button">
						<button @click="search" v-bind:title="checkTips">查询</button>
						<button @click="save" v-bind:title="saveTips">保存</button>
						<button @click="reset" v-bind:title="resetTips">重置</button>
					</div>
				</div>
			</div>

			<div class="showResult">
				<!-- 地图容器 -->
				<div class="showMap">
					<div class="baiduMapImage" id="fishGroundMap"></div>
				</div>

				<!-- 统计结果容器 -->
				<div class="showStatistics">
					<div class="topEcharts">
						<div class="illLineTitle">非法作业统计折线图</div>
						<div class="lineEcharts" id="lineEchartsId"></div>
					</div>
					<div class="bottomEcharts">
						<div class="illPieTitle">非法作业统计占比图</div>
						<div class="pieEcharts" id="pieEchartsId"></div>
					</div>
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
				thisPageTips: '张网作业方式统计分析',
				backTips: '后退',
				homeTips: '主页',
				selectFGTips: '请选择渔场',
				startDateTips: '请选择起始日期',
				endDateTips: '请选择终止日期',
				checkTips: '查询结果',
				resetTips: '重置条件',
				saveTips: '保存结果',

				// 默认渔场设置
				fishGround: '所有渔场',
				// 渔场列表
				FGoptions: [{
					value: '所有渔场'
				},
					{
						value: '舟山渔场'
					},
					{
						value: '舟外渔场'
					},
					{
						value: '长江口渔场'
					},
					{
						value: '江外渔场'
					},
					{
						value: '吕泗渔场'
					},
					{
						value: '大沙渔场'
					},
					{
						value: '鱼山渔场'
					},
					{
						value: '鱼外渔场'
					},
					{
						value: '温台渔场'
					},
					{
						value: '温外渔场'
					},
					{
						value: '闽东渔场'
					},
					{
						value: '闽外渔场'
					}
				],
				// 默认时间设置
				startDate: '2019-07-15',
				endDate: '2019-08-31',

				//渔场地图信息
				centerPoint: '',//地图中心点坐标
				centerLng: "124.80",//地图中心经纬度
				centerLat: "30.60",
				level: "6",//地图级别
				selectedBorderColor: 'red',//渔场区域选中颜色
				selectedBorderWidth: 4,//渔场区域选中框宽度
				forbidLable: 0,//是否禁止区域被清除
				defaultBorderColor: 'gray',//渔场区域默认边框颜色
				defaultBorderWidth: 2,
				//渔场经纬度位置信息
				/*fishArea:[
					{leftLng:120.2,leftLat:32,rightLng:122.5,rightLat:34},//吕泗渔场
					{leftLng:122.5,leftLat:32,rightLng:125,rightLat:34},//大沙渔场
					{leftLng:122.2,leftLat:31,rightLng:125,rightLat:32},//长江口渔场
					{leftLng:121.8,leftLat:29.5,rightLng:125,rightLat:31},//舟山渔场
					{leftLng:121,leftLat:28,rightLng:125,rightLat:29.5},//鱼山渔场
					{leftLng:120,leftLat:27,rightLng:125,rightLat:28},//温台渔场
					{leftLng:125,leftLat:32,rightLng:128,rightLat:34},//沙外渔场
					{leftLng:125,leftLat:31,rightLng:128,rightLat:32},//江外渔场
					{leftLng:125,leftLat:29.5,rightLng:128,rightLat:31},//舟外渔场
					{leftLng:125,leftLat:28,rightLng:127,rightLat:29.5},//鱼外渔场
					{leftLng:125,leftLat:27,rightLng:127.5,rightLat:28},//温外渔场
				],*/

				//非法作业折线图选项
				LineOption: {
					color: "#f44",
					tooltip: {
						trigger: "axis",
						axisPointer: {
							type: "line",
							lineStyle: {
								type: "dashed",
							},
						},
					},
					grid: {
						left: "10%",
						top: "10%",
						width: "80%",
						height: "70%",
					},
					xAxis: {
						// name: "日期",
						// nameLocation: "middle",
						// nameTextStyle: {
						// 	color: "#58a0ee",
						// 	fontWeight: "normal",
						// 	fontSize: 12,
						// },
						// nameGap: 20,
						type: "category",
						// data 为横坐标
						axisTick: {
							show: true,
							inside: true,
							alignWithLabel: true,

						},
						axisLine: {
							lineStyle: {
								color: "#58a0ee",
								width: 2,
							}
						},
						axisLabel: {
							fontSize: 14,
							color: "#58a0ee",
							rotate: 0,
						},
						splitLine: {
							show: false,
						},
					},
					yAxis: {
						// name: "次数",
						// nameLocation: "middle",
						// nameTextStyle: {
						// 	color: "#58a0ee",
						// 	fontWeight: "normal",
						// 	fontSize: 12,
						// },
						// nameGap: 16,
						minInterval: 1,
						type: "value",
						axisTick: {
							show: true,
							inside: true,
							alignWithLabel: true,
						},
						axisLine: {
							lineStyle: {
								color: "#58a0ee",
								width: 2,
							}
						},
						scale: false,
						axisLabel: {
							fontSize: 14,
							color: "#58a0ee",
							rotate: 0,
						},
						splitLine: {
							show: true,
							lineStyle: {
								color: "#ccc",
								width: 1,
								type: "dashed",
							},
						},
					},
					series: [{
						name: "非法作业次数",
						type: "line",
						symbolSize: 2,
						showSymbol: false,
						cursor: 'pointer',
						step: false,
						smooth: false,
						lineStyle: {
							color: "#ed7d31",
							width: 2,
						},
					}],

				},
				//非法作业占比图选项
				PieOption: {
					tooltip: {
						trigger: "item",
						formatter: "{a} <br/>{b} : {c} ({d}%)"
					},
					grid: {
						left: "30%",
						top: "0%",
						width: "80%",
						height: "80%",
					},
					legend: {
						orient: "vertical",
						left: "10%",
						top: "middle",
						data: ["正常作业", "非法作业"],
						textStyle: {
							color: "default",
							fontSize: 16,
						}
					},
					series: [{
						name: "占比情况",
						type: "pie",
						radius: "80%",
						center: ["66.15%", "50%"],
						label: {
							show: false,
							fontSize: 25,
						},
						data: [],
						itemStyle: {
							emphasis: {
								shadowBlur: 10,
								shadowOffsetX: 0,
								shadowColor: "rgba(0, 0, 0, 0.5)"
							}
						}
					}],
					color: ["#5b9bff", "#ed7d31"]
				},
				// 存储数据长度
				dataLength: 0,
				//存储正常作业与非法作业次数
				pieData: {
					normal: {
						value: 0,
						name: '正常作业',
					},
					illegal: {
						value: 0,
						name: '非法作业',
					},
				},
				//存储非法作业数据
				illDataByDate: [],
				//存储日期序列
				dateArray: [],
			};
		},


		mounted() {
			this.init();
			// this.baiduMap();
			this.fishMap(this.centerLng, this.centerLat, this.level);
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
			init() {
				var myDate = new Date();
				console.log("----------" + myDate.toLocaleString() + "----------");
				console.log("正在执行init()...");
				this.dataAskDeal();
			},

			//数据请求及返回数据处理
			dataAskDeal() {
				console.log("正在执行dataAskDeal()...");
				console.log("startDate:" + this.startDate);
				console.log("endDate:" + this.endDate);

				this.dateArray = [];
				var i = 0;
				var startTime = this.getDate(this.startDate);
				var endTime = this.getDate(this.endDate);
				while ((endTime.getTime() - startTime.getTime()) >= 0) {
					var year = startTime.getFullYear();
					var x = startTime.getMonth() + 1;
					var month = x.toString().length == 1 ? "0" + x.toString() : x;
					var day = startTime.getDate().toString().length == 1 ? "0" + startTime.getDate() : startTime.getDate();
					//保存时间序列
					this.dateArray[i] = year + '-' + month + '-' + day;
					i += 1;
					startTime.setDate(startTime.getDate() + 1);
				}
				this.axios({
					method: "post",
					url: "/getDataByMonthOrDay",
					data: {
						jobType: '张网',
						startTime: this.startDate + ' 00:00:00',
						endTime: this.endDate + ' 23:59:59',
						byDay: 1
					},
				}).then((response) => {
					console.log("响应正常...");
					var sum_nor = 0;
					var sum_ill = 0;
					var length = response.data.total.length;
					this.dataLength = length;
					console.log("数据总长度：" + length);
					for (var i = 0; i < response.data.normal.length; i++) {
						sum_nor += response.data.normal[i];
					}
					;
					for (var i = 0; i < response.data.illegal.length; i++) {
						sum_ill += response.data.illegal[i];
					}
					;
					console.log("正常总数:" + sum_nor);
					console.log("非法总数:" + sum_ill);
					this.pieData.normal.value = sum_nor;
					this.pieData.illegal.value = sum_ill;
					this.illDataByDate = response.data.illegal;
					this.drawStowCharts(length);
				}).catch((response) => {
					console.log("响应错误...");
					console.log("错误是：" + response);
				})
			},

			//String to Date 时间调整函数 月份-1，适配Date类型
			getDate(datestr) {
				var temp = datestr.split("-");
				var date = new Date(temp[0], temp[1] - 1, temp[2]);
				return date;
			},

			drawStowCharts(len) {
				var lineEchart = this.$echarts.init(
					document.getElementById("lineEchartsId")
				);
				var pieEchart = this.$echarts.init(
					document.getElementById("pieEchartsId")
				);

				console.log("echarts实例创建完成");
				lineEchart.setOption(this.LineOption);
				lineEchart.setOption({
					xAxis: {
						data: this.dateArray,
						axisLabel: {
							interval: function (idx, val) {
								if (idx == 0 || idx == Math.floor((len - 1) / 3) || idx == Math.floor((len - 1) / 3 * 2) || idx == len - 1) {
									return true;
								}
							},
						},
					},
					series: {
						data: this.illDataByDate,
					},
				});


				pieEchart.setOption(this.PieOption);
				pieEchart.setOption({
					series: {

						data: [this.pieData.normal, this.pieData.illegal],
					}
				});

				// pieEchart.showLoading();
				window.addEventListener("resize", function () {
					lineEchart.resize();
				});
				window.addEventListener("resize", function () {
					pieEchart.resize();
				});
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
				console.log("我要保存报告到本地！");
				// 先查询，后保存
				var nowTime = new Date();
				this.exportData("张网统计报告 " + nowTime.toLocaleString() + ".txt", this.dealDataToExport());
			},
			//重置方法 恢复到默认渔场、默认时间
			reset() {
				this.fishGround = '所有渔场';
				this.startDate = '2019-07-15';
				this.endDate = '2019-08-31';
				this.dataAskDeal();
			},

			fake_click(obj) {
				var ev = document.createEvent("MouseEvents");
				ev.initMouseEvent(
					"click", true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null
				);
				obj.dispatchEvent(ev);
			},

			exportData(name, data) {
				var urlObject = window.URL || window.webkitURL || window;
				var downloadData = new Blob([data]);
				var save_link = document.createElementNS("http://www.w3.org/1999/xhtml", "a");
				save_link.href = urlObject.createObjectURL(downloadData);
				save_link.download = name;
				this.fake_click(save_link);
			},

			dealDataToExport() {
				var datatoexport = "";
				var allsum = this.pieData.normal.value + this.pieData.illegal.value;
				var normalPer = (this.pieData.normal.value / allsum * 100).toFixed(2);
				var illegalPer = (this.pieData.illegal.value / allsum * 100).toFixed(2);
				datatoexport += "作业方式：张网\n"
				datatoexport += "渔场范围：" + this.fishGround + "\n";
				datatoexport += "时间范围：" + this.startDate + "\t到\t" + this.endDate + "\n\n";
				datatoexport += "总作业次数：" + allsum
					+ "\t正常作业次数：" + this.pieData.normal.value + "\t非法作业次数：" + this.pieData.illegal.value + "\n";
				datatoexport += "正常作业占比：" + normalPer + "%";
				datatoexport += "\t非法作业占比：" + illegalPer + "%" + "\n\n";
				var i;
				datatoexport += "时间" + "\t\t\t" + "违法作业次数\n"
				for (i = 0; i < this.dataLength; i++) {
					datatoexport += this.dateArray[i] + "\t\t" + this.illDataByDate[i] + "\n";
				}

				return datatoexport;
			},

			//渔场地图显示


			//显示单个渔场网格框（矩形区域）
			fishgrid(leftLng, downLat, rightLng, upLat, color, bordWidth, forbid) {
				var pStart, pEnd, rectangle;
				pStart = new BMap.Point(leftLng, downLat);
				pEnd = new BMap.Point(rightLng, upLat);
				rectangle = new BMap.Polygon([
					new BMap.Point(pStart.lng, pStart.lat),
					new BMap.Point(pEnd.lng, pStart.lat),
					new BMap.Point(pEnd.lng, pEnd.lat),
					new BMap.Point(pStart.lng, pEnd.lat)
				], {strokeColor: color, strokeWeight: bordWidth, strokeStyle: 'dashed'});
				rectangle.setFillColor('none');//设置覆盖物透明
				if (forbid) {
					rectangle.disableMassClear();
				}
				map.addOverlay(rectangle);
			},
			//显示区域直线
			linefish(startLng, startLat, endLng, endLat, color, bordWidth, forbid) {
				var line;
				line = new BMap.Polyline([
					new BMap.Point(startLng, startLat),
					new BMap.Point(endLng, endLat)
				], {strokeColor: color, strokeWeight: bordWidth, strokeStyle: 'dashed'});
				if (forbid) {
					line.disableMassClear();
				}
				map.addOverlay(line);
			},
			//通过文本框选择渔场区域在地图中显示
			showFishArea() {
				if (this.fishMode == '吕泗渔场') {
					map.clearOverlays();//清除渔场区域范围
					var infoWindow = new BMap.InfoWindow("左经度:120.2 下纬度:32" + '<br />' +
						"右经度:122.5 上纬度:34", {title: "吕泗渔场", width: 290, height: 100});
					var changeCenterPoint = new BMap.Point(121.3, 33);//创建点坐标
					/*map.setCenter(changeCenterPoint);//设置地图中心点
					map.setZoom(9);//设置地图级别*/
					map.openInfoWindow(infoWindow, changeCenterPoint);//显示经纬度信息和渔场名称
					this.forbidLable = 0;
					this.fishgrid(120.2, 32, 122.5, 34, this.selectedBorderColor, this.selectedBorderWidth, this.forbidLable);//显示渔场范围
				} else if (this.fishMode == '大沙渔场') {
					map.clearOverlays();//清除渔场区域范围
					var infoWindow = new BMap.InfoWindow("左经度:122.5 下纬度:32" + '<br />' +
						"右经度:125 上纬度:34", {title: "大沙渔场", width: 290, height: 100});
					var changeCenterPoint = new BMap.Point(123.7, 33);//创建点坐标
					/*map.setCenter(changeCenterPoint);//设置地图中心点
					map.setZoom(9);//设置地图级别*/
					map.openInfoWindow(infoWindow, changeCenterPoint);//显示经纬度信息和渔场名称
					this.forbidLable = 0;
					this.fishgrid(122.5, 32, 125, 34, this.selectedBorderColor, this.selectedBorderWidth, this.forbidLable);//显示渔场范围
				} else if (this.fishMode == '长江口渔场') {
					map.clearOverlays();//清除渔场区域范围
					var infoWindow = new BMap.InfoWindow("左经度:122.0 下纬度:31" + '<br />' +
						"右经度:125 上纬度:32", {title: "长江口渔场", width: 290, height: 100});
					var changeCenterPoint = new BMap.Point(123.5, 31.5);//创建点坐标
					/*map.setCenter(changeCenterPoint);//设置地图中心点
					map.setZoom(9);//设置地图级别*/
					map.openInfoWindow(infoWindow, changeCenterPoint);//显示经纬度信息和渔场名称
					this.forbidLable = 0;
					this.fishgrid(122, 31, 125, 32, this.selectedBorderColor, this.selectedBorderWidth, this.forbidLable);//显示渔场范围
				} else if (this.fishMode == '舟山渔场') {
					map.clearOverlays();//清除渔场区域范围
					var infoWindow = new BMap.InfoWindow("左经度:121.8 下纬度:29.5" + '<br />' +
						"右经度:125 上纬度:31", {title: "舟山渔场", width: 290, height: 100});
					var changeCenterPoint = new BMap.Point(123.6, 30.5);//创建点坐标
					/*map.setCenter(changeCenterPoint);//设置地图中心点
					map.setZoom(9);//设置地图级别*/
					map.openInfoWindow(infoWindow, changeCenterPoint);//显示经纬度信息和渔场名称
					this.forbidLable = 0;
					this.fishgrid(121.8, 29.5, 125, 31, this.selectedBorderColor, this.selectedBorderWidth, this.forbidLable);//显示渔场范围
				} else if (this.fishMode == '鱼山渔场') {
					map.clearOverlays();//清除渔场区域范围
					var infoWindow = new BMap.InfoWindow("左经度:121 下纬度:28" + '<br />' +
						"右经度:125 上纬度:29.5", {title: "鱼山渔场", width: 290, height: 100});
					var changeCenterPoint = new BMap.Point(123, 28.7);//创建点坐标
					/*map.setCenter(changeCenterPoint);//设置地图中心点
					map.setZoom(9);//设置地图级别*/
					map.openInfoWindow(infoWindow, changeCenterPoint);//显示经纬度信息和渔场名称
					this.forbidLable = 0;
					this.fishgrid(121, 28, 125, 29.5, this.selectedBorderColor, this.selectedBorderWidth, this.forbidLable);//显示渔场范围
				} else if (this.fishMode == '温台渔场') {
					map.clearOverlays();//清除渔场区域范围
					var infoWindow = new BMap.InfoWindow("左经度:120 下纬度:27" + '<br />' +
						"右经度:125 上纬度:28", {title: "温台渔场", width: 290, height: 100});
					var changeCenterPoint = new BMap.Point(122.5, 27.5);//创建点坐标
					/*map.setCenter(changeCenterPoint);//设置地图中心点
					map.setZoom(9);//设置地图级别*/
					map.openInfoWindow(infoWindow, changeCenterPoint);//显示经纬度信息和渔场名称
					this.forbidLable = 0;
					this.fishgrid(120, 27, 125, 28, this.selectedBorderColor, this.selectedBorderWidth, this.forbidLable);//显示渔场范围
				} else if (this.fishMode == '沙外渔场') {
					map.clearOverlays();//清除渔场区域范围
					var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:32" + '<br />' +
						"右经度:128 上纬度:34", {title: "沙外渔场", width: 290, height: 100});
					var changeCenterPoint = new BMap.Point(126.5, 33);//创建点坐标
					/*map.setCenter(changeCenterPoint);//设置地图中心点
					map.setZoom(9);//设置地图级别*/
					map.openInfoWindow(infoWindow, changeCenterPoint);//显示经纬度信息和渔场名称
					this.forbidLable = 0;
					this.fishgrid(125, 32, 128, 34, this.selectedBorderColor, this.selectedBorderWidth, this.forbidLable);//显示渔场范围
				} else if (this.fishMode == '江外渔场') {
					map.clearOverlays();//清除渔场区域范围
					var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:31" + '<br />' +
						"右经度:129 上纬度:32", {title: "江外渔场", width: 290, height: 100});
					var changeCenterPoint = new BMap.Point(127, 31.5);//创建点坐标
					/*map.setCenter(changeCenterPoint);//设置地图中心点
					map.setZoom(9);//设置地图级别*/
					map.openInfoWindow(infoWindow, changeCenterPoint);//显示经纬度信息和渔场名称
					this.forbidLable = 0;
					this.fishgrid(125, 31, 128, 32, this.selectedBorderColor, this.selectedBorderWidth, this.forbidLable);//显示渔场范围
				} else if (this.fishMode == '舟外渔场') {
					map.clearOverlays();//清除渔场区域范围
					var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:29.5" + '<br />' +
						"右经度:128 上纬度:31", {title: "舟外渔场", width: 290, height: 100});
					var changeCenterPoint = new BMap.Point(126.5, 30.5);//创建点坐标
					/*map.setCenter(changeCenterPoint);//设置地图中心点
					map.setZoom(9);//设置地图级别*/
					map.openInfoWindow(infoWindow, changeCenterPoint);//显示经纬度信息和渔场名称
					this.forbidLable = 0;
					this.fishgrid(125, 29.5, 128, 31, this.selectedBorderColor, this.selectedBorderWidth, this.forbidLable);//显示渔场范围
				} else if (this.fishMode == '鱼外渔场') {
					map.clearOverlays();//清除渔场区域范围
					var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:28" + '<br />' +
						"右经度:127 上纬度:29.5", {title: "鱼外渔场", width: 290, height: 100});
					var changeCenterPoint = new BMap.Point(126, 28.5);//创建点坐标
					/*map.setCenter(changeCenterPoint);//设置地图中心点
					map.setZoom(9);//设置地图级别*/
					map.openInfoWindow(infoWindow, changeCenterPoint);//显示经纬度信息和渔场名称
					this.forbidLable = 0;
					this.fishgrid(125, 28, 127, 29.5, this.selectedBorderColor, this.selectedBorderWidth, this.forbidLable);//显示渔场范围
				} else if (this.fishMode == '温外渔场') {
					map.clearOverlays();//清除渔场区域范围
					var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:27" + '<br />' +
						"右经度:127.5 上纬度:28", {title: "温外渔场", width: 290, height: 100});
					var changeCenterPoint = new BMap.Point(126, 27.5);//创建点坐标
					/*map.setCenter(changeCenterPoint);//设置地图中心点
					map.setZoom(9);//设置地图级别*/
					map.openInfoWindow(infoWindow, changeCenterPoint);//显示经纬度信息和渔场名称
					this.forbidLable = 0;
					this.fishgrid(125, 27, 127.5, 28, this.selectedBorderColor, this.selectedBorderWidth, this.forbidLable);//显示渔场范围
				} else {
					map.clearOverlays();//清除渔场区域范围
					//this.fullShow();
				}
			},
			//整个渔场范围显示
			fullShow() {
				this.forbidLable = 1;
				/*this.fishgrid(120.2,32,122.5,34,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);//吕泗渔场
				this.fishgrid(122.5,32,125,34,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);//大沙渔场
				this.fishgrid(122,31,125,32,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);//长江口渔场
				this.fishgrid(121.8,29.5,125,31,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);//舟山渔场
				this.fishgrid(121,28,125,29.5,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);//鱼山渔场
				this.fishgrid(120,27,125,28,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);//温台渔场
				this.fishgrid(125,32,128,34,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);//沙外渔场
				this.fishgrid(125,31,128,32,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);//江外渔场
				this.fishgrid(125,29.5,128,31,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);//舟外渔场
				this.fishgrid(125,28,127,29.5,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);//鱼外渔场
				this.fishgrid(125,27,127.5,28,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);//温外渔场*/
				var fullArea = new BMap.Polygon([
					new BMap.Point(120.2, 34),
					new BMap.Point(120.2, 32),//渔场外边缘坐标点
					new BMap.Point(122, 32),
					new BMap.Point(122, 31),
					new BMap.Point(121.8, 31),
					new BMap.Point(121.8, 29.5),
					new BMap.Point(121, 29.5),
					new BMap.Point(121, 28),
					new BMap.Point(120, 28),
					new BMap.Point(120, 27),
					new BMap.Point(127.5, 27),
					new BMap.Point(127.5, 28),
					new BMap.Point(127, 28),
					new BMap.Point(127, 29.5),
					new BMap.Point(128, 29.5),
					new BMap.Point(128, 34),
				], {
					strokeColor: this.defaultBorderColor,
					strokeWeight: this.defaultBorderWidth,
					strokeStyle: 'dashed'
				});
				fullArea.setFillColor('none');
				fullArea.disableMassClear();
				map.addOverlay(fullArea);
				/*画出渔场区域内的线段*/
				this.linefish(125, 34, 125, 27, this.defaultBorderColor, this.defaultBorderWidth, this.forbidLable);
				this.linefish(125, 32, 128, 32, this.defaultBorderColor, this.defaultBorderWidth, this.forbidLable);
				this.linefish(125, 31, 128, 31, this.defaultBorderColor, this.defaultBorderWidth, this.forbidLable);
				this.linefish(125, 29.5, 127, 29.5, this.defaultBorderColor, this.defaultBorderWidth, this.forbidLable);
				this.linefish(125, 28, 127, 28, this.defaultBorderColor, this.defaultBorderWidth, this.forbidLable);
				this.linefish(122.5, 34, 122.5, 32, this.defaultBorderColor, this.defaultBorderWidth, this.forbidLable);
				this.linefish(122, 32, 125, 32, this.defaultBorderColor, this.defaultBorderWidth, this.forbidLable);
				this.linefish(122, 31, 125, 31, this.defaultBorderColor, this.defaultBorderWidth, this.forbidLable);
				this.linefish(121.8, 29.5, 125, 29.5, this.defaultBorderColor, this.defaultBorderWidth, this.forbidLable);
				this.linefish(121, 28, 125, 28, this.defaultBorderColor, this.defaultBorderWidth, this.forbidLable);
			},
			//渔场地图
			fishMap(lng, lat, level) {
				var map = new BMap.Map('fishGroundMap', {enableMapClick: false}); //创建地图实例
				this.centerpoint = new BMap.Point(lng, lat);//创建点坐标
				map.addControl(
					new BMap.MapTypeControl({
						mapTypes: [
							BMAP_NORMAL_MAP,
							BMAP_SATELLITE_MAP,
							BMAP_HYBRID_MAP
						]
					})
				);
				map.centerAndZoom(this.centerpoint, level);//初始化地图，设置中心点坐标和地图级别
				map.enableScrollWheelZoom(true);//开启鼠标滚轮缩放
				window.map = map;//存储map变量
				var _this = this;
				//默认显示整个渔场范围
				this.fullShow();
				//点击显示渔场信息和经纬度
				map.addEventListener("click", function (click) {
					if (click.point.lng >= 120.2 && click.point.lng <= 122.5 &&
						click.point.lat >= 32 && click.point.lat <= 34) {
						map.clearOverlays();//清除渔场区域范围
						var infoWindow = new BMap.InfoWindow("左经度:120.2 下纬度:32" + '<br />' +
							"右经度:122.5 上纬度:34", {title: "吕泗渔场", width: 290, height: 100});
						map.openInfoWindow(infoWindow, click.point);
						_this.forbidLable = 0;
						_this.fishgrid(120.2, 32, 122.5, 34, _this.selectedBorderColor, _this.selectedBorderWidth, _this.forbidLable);//this在map中的作用域出问题，需先声明
					} else if (click.point.lng >= 122.5 && click.point.lng <= 125 &&
						click.point.lat >= 32 && click.point.lat <= 34) {
						map.clearOverlays();//清除渔场区域范围
						var infoWindow = new BMap.InfoWindow("左经度:122.5 下纬度:32" + '<br />' +
							"右经度:125 上纬度:34", {title: "大沙渔场", width: 290, height: 100});
						map.openInfoWindow(infoWindow, click.point);
						_this.forbidLable = 0;
						_this.fishgrid(122.5, 32, 125, 34, _this.selectedBorderColor, _this.selectedBorderWidth, _this.forbidLable);//this在map中的作用域出问题，需先声明
					} else if (click.point.lng >= 122 && click.point.lng <= 125 &&
						click.point.lat >= 31 && click.point.lat <= 32) {
						map.clearOverlays();//清除渔场区域范围
						var infoWindow = new BMap.InfoWindow("左经度:122 下纬度:31" + '<br />' +
							"右经度:125 上纬度:32", {title: "长江口渔场", width: 290, height: 100});
						map.openInfoWindow(infoWindow, click.point);
						_this.forbidLable = 0;
						_this.fishgrid(122, 31, 125, 32, _this.selectedBorderColor, _this.selectedBorderWidth, _this.forbidLable);//this在map中的作用域出问题，需先声明
					} else if (click.point.lng >= 121.8 && click.point.lng <= 125 &&
						click.point.lat >= 29.5 && click.point.lat <= 31) {
						map.clearOverlays();//清除渔场区域范围
						var infoWindow = new BMap.InfoWindow("左经度:121.8 下纬度:29.5" + '<br />' +
							"右经度:125 上纬度:31", {title: "舟山渔场", width: 290, height: 100});
						map.openInfoWindow(infoWindow, click.point);
						_this.forbidLable = 0;
						_this.fishgrid(121.8, 29.5, 125, 31, _this.selectedBorderColor, _this.selectedBorderWidth, _this.forbidLable);//this在map中的作用域出问题，需先声明
					} else if (click.point.lng >= 121 && click.point.lng <= 125 &&
						click.point.lat >= 28 && click.point.lat <= 29.5) {
						map.clearOverlays();//清除渔场区域范围
						var infoWindow = new BMap.InfoWindow("左经度:121 下纬度:28" + '<br />' +
							"右经度:125 上纬度:29.5", {title: "鱼山渔场", width: 290, height: 100});
						map.openInfoWindow(infoWindow, click.point);
						_this.forbidLable = 0;
						_this.fishgrid(121, 28, 125, 29.5, _this.selectedBorderColor, _this.selectedBorderWidth, _this.forbidLable);//this在map中的作用域出问题，需先声明
					} else if (click.point.lng >= 120 && click.point.lng <= 125 &&
						click.point.lat >= 27 && click.point.lat <= 28) {
						map.clearOverlays();//清除渔场区域范围
						var infoWindow = new BMap.InfoWindow("左经度:120 下纬度:27" + '<br />' +
							"右经度:125 上纬度:28", {title: "温台渔场", width: 290, height: 100});
						map.openInfoWindow(infoWindow, click.point);
						_this.forbidLable = 0;
						_this.fishgrid(120, 27, 125, 28, _this.selectedBorderColor, _this.selectedBorderWidth, _this.forbidLable);//this在map中的作用域出问题，需先声明
					} else if (click.point.lng >= 125 && click.point.lng <= 128 &&
						click.point.lat >= 32 && click.point.lat <= 34) {
						map.clearOverlays();//清除渔场区域范围
						var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:32" + '<br />' +
							"右经度:128 上纬度:34", {title: "沙外渔场", width: 290, height: 100});
						map.openInfoWindow(infoWindow, click.point);
						_this.forbidLable = 0;
						_this.fishgrid(125, 32, 128, 34, _this.selectedBorderColor, _this.selectedBorderWidth, _this.forbidLable);//this在map中的作用域出问题，需先声明
					} else if (click.point.lng >= 125 && click.point.lng <= 128 &&
						click.point.lat >= 31 && click.point.lat <= 32) {
						map.clearOverlays();//清除渔场区域范围
						var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:31" + '<br />' +
							"右经度:128 上纬度:32", {title: "江外渔场", width: 290, height: 100});
						map.openInfoWindow(infoWindow, click.point);
						_this.forbidLable = 0;
						_this.fishgrid(125, 31, 128, 32, _this.selectedBorderColor, _this.selectedBorderWidth, _this.forbidLable);//this在map中的作用域出问题，需先声明
					} else if (click.point.lng >= 125 && click.point.lng <= 128 &&
						click.point.lat >= 29.5 && click.point.lat <= 31) {
						map.clearOverlays();//清除渔场区域范围
						var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:29.5" + '<br />' +
							"右经度:128 上纬度:31", {title: "舟外渔场", width: 290, height: 100});
						map.openInfoWindow(infoWindow, click.point);
						_this.forbidLable = 0;
						_this.fishgrid(125, 29.5, 128, 31, _this.selectedBorderColor, _this.selectedBorderWidth, _this.forbidLable);//this在map中的作用域出问题，需先声明
					} else if (click.point.lng >= 125 && click.point.lng <= 127 &&
						click.point.lat >= 28 && click.point.lat <= 29.5) {
						map.clearOverlays();//清除渔场区域范围
						var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:28" + '<br />' +
							"右经度:127 上纬度:29.5", {title: "鱼外渔场", width: 290, height: 100});
						map.openInfoWindow(infoWindow, click.point);
						_this.forbidLable = 0;
						_this.fishgrid(125, 28, 127, 29.5, _this.selectedBorderColor, _this.selectedBorderWidth, _this.forbidLable);//this在map中的作用域出问题，需先声明
					} else if (click.point.lng >= 125 && click.point.lng <= 127.5 &&
						click.point.lat >= 27 && click.point.lat <= 28) {
						map.clearOverlays();//清除渔场区域范围
						var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:27" + '<br />' +
							"右经度:127.5 上纬度:28", {title: "温外渔场", width: 290, height: 100});
						map.openInfoWindow(infoWindow, click.point);
						_this.forbidLable = 0;
						_this.fishgrid(125, 27, 127.5, 28, _this.selectedBorderColor, _this.selectedBorderWidth, _this.forbidLable);//this在map中的作用域出问题，需先声明
					} else {
						map.clearOverlays();//清除渔场区域范围
					}
				});
			},

			/*百度地图*/
			baiduMap() {
				//创建实例
				var map = new BMap.Map("fishGroundMap");
				//创建坐标点
				var point = new BMap.Point(122.20, 30.00);
				map.addControl(new BMap.MapTypeControl({
					mapTypes: [
						BMAP_NORMAL_MAP,
						BMAP_SATELLITE_MAP,
						BMAP_HYBRID_MAP
					]
				}));
				// map.addControl(new BMap.NavigationControl()); 缩放比例尺

				//初始化实例，传入坐标点并设置地图级别
				map.centerAndZoom(point, 12);
				map.enableScrollWheelZoom(true);
				window.map = map; //将map变量存储在全局

				/* 渔船标注位置*/
				// this.addMarker();
			},
		},

	};
</script>


<style scoped>
	/* 底层背景样式，窗口自适应 */
	/* 底层背景 */
	#main-bkground {
		position: absolute;
		width: 100%;
		height: 100%;
		overflow: hidden;
		/* 底层背景层 */
		z-index: 1;
	}

	/* 底层背景图片 */
	#main-bkground .img {
		width: 100%;
		height: 100%;
		/* 底层背景图片层 */
		z-index: 2;
	}

	/* 主要内容样式，窗口自适应 */
	/* 主要内容 */
	#main-content {
		position: absolute;
		width: 100%;
		height: 100%;
		overflow: hidden;
		/* 内容层 */
		z-index: 90;
	}

	/* 中心标题样式 */
	#main-content .centTitle {
		position: absolute;
		left: 30vw;
		width: 40vw;
		top: 1.2vh;
		height: 5.5vh;
		/* background-color 测试用 */
		/* background-color: #FFFFFF; */
	}

	#main-content .centTitle .myp1 {
		font-family: FZDHTJW--GB1-0;
		color: #58a0ee;
		cursor: pointer;
		position: absolute;
		letter-spacing: 0.2vw;
		left: 20%;
		width: 60%;
		height: 80%;
		top: -5%;
		font-size: 4.5vh;
	}

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

	/* 菜单栏 */
	#main-content .setMenu {
		position: absolute;
		width: 80vw;
		height: 6vh;
		left: 10vw;
		top: 12vh;
		margin-top: 1vh;
		margin-bottom: 1vh;
		color: #58a0ee;
		/* background-color 测试用 */
		/* background-color: #FFFFFF; */
	}

	/*选择渔场菜单*/
	#main-content .setMenu .selectFG {
		position: absolute;
		width: 18%;
		height: 100%;
		color: #58a0ee;
		font-size: 2.8vh;
		/* background-color 测试用 */
		/* background-color: #0e0270; */
	}

	#main-content .setMenu .selectFG form select {
		width: 60%;
		height: 5vh;
		background-color: #0e0270;
		color: #58a0ee;
		border-color: #58a0ee;
		border-width: 1px;
	}


	/* 选择日期时间菜单 */
	#main-content .setMenu .selectDT {
		position: absolute;
		left: 20%;
		width: 50%;
		height: 100%;
		font-size: 2.8vh;
		/* background-color 测试用 */
		/* background-color: #0e0270; */
	}

	#main-content .setMenu .selectDT form input {
		width: 35%;
		height: 5vh;
		background-color: #0e0270;
		color: #58a0ee;
		border-color: #58a0ee;
	}

	/*  修改日历控件类型 */
	/*控制编辑区域的*/
	input[type="date"]::-webkit-datetime-edit {
	}

	/*控制年月日这个区域的*/
	input[type="date"]::-webkit-datetime-edit-fields-wrapper {
	}

	/*这是控制年月日之间的斜线或短横线的*/
	input[type="date"]::-webkit-datetime-edit-text {
		color: #58a0ee;
		padding: 0.5em;
	}

	/*控制年文字, 如2019四个字母占据的那片地方*/
	input[type="date"]::-webkit-datetime-edit-year-field {
		color: #58a0ee;
		background-color: #0e0270;
	}

	/*控制月文字*/
	input[type="date"]::-webkit-datetime-edit-month-field {
		color: #58a0ee;
		background-color: #0e0270;
	}

	/*控制日文字*/
	input[type="date"]::-webkit-datetime-edit-day-field {
		color: #58a0ee;
		background-color: #0e0270;
	}

	/*这是控制上下小箭头的*/
	input[type="date"]::-webkit-inner-spin-button {
		/* 直接隐藏 */
		visibility: hidden;
	}

	/*这是控制下拉小箭头的*/
	input[type="date"]::-webkit-calendar-picker-indicator {
		position: relative;
		right: 2%;
		border: 0.1em solid #58a0ee;
		border-radius: 0.2em;
		color: #0e0270;
		background-image: -webkit-linear-gradient(top, #58a0ee, #acc8e6);
	}

	/*控制清除按钮*/
	input[type="date"]::-webkit-clear-button {
		/* 直接隐藏 */
		visibility: hidden;
	}

	#main-content .setMenu .menuButtons {
		position: absolute;
		left: 75%;
		width: 25%;
		height: 100%;
		font-size: 2.8vh;
		/* background-color 测试用 */
		/* background-color: #0e0270; */
	}

	#main-content .setMenu .menuButtons button {
		width: 24%;
		height: 5vh;
		background-color: #0e0270;
		color: #58a0ee;
		border-color: #58a0ee;
		cursor: pointer;
	}

	/* 显示结果区域 */
	#main-content .showResult {
		position: absolute;
		width: 90vw;
		height: 74vh;
		left: 5vw;
		top: 18vh;
		margin-top: 0vh;
		margin-bottom: 0vh;
		/* background-color 测试用 */
		/* background-color: #FFFFFF; */

	}

	#main-content .showMap {
		position: absolute;
		width: 60%;
		height: 95%;
		left: 2%;
		top: 5%;
		/* background-color 测试用 */
		/* background-color: #EFAB00; */
	}

	#main-content .showMap .baiduMapImage {
		position: absolute;
		width: 100%;
		height: 100%;
		margin: 0px;
	}

	#main-content .showStatistics {
		position: absolute;
		width: 33%;
		height: 95%;
		right: 2%;
		top: 5%;
		/* background-color 测试用 */
		/* background-color: #FFFFFF; */
	}


	#main-content .showStatistics .topEcharts {
		position: absolute;
		top: 2%;
		left: 0%;
		height: 50%;
		width: 100%;
		/* background-color 测试用 */
		/* background-color: greenyellow; */
	}

	#main-content .showStatistics .topEcharts .illLineTitle {
		position: absolute;
		top: -5%;
		left: 20%;
		height: 5%;
		width: 60%;
		font-size: 2.8vh;
		letter-spacing: 0.1vw;
		color: #58a0ee;
		/* background-color 测试用 */
		/* background-color: #0000FF; */
	}

	#main-content .showStatistics .topEcharts .lineEcharts {
		position: absolute;
		top: 6%;
		height: 94%;
		left: 0%;
		width: 100%;
		/* background-color 测试用 */
		/* background-color: #FFFFFF; */
	}

	#main-content .showStatistics .bottomEcharts {
		position: absolute;
		top: 55%;
		left: 0%;
		height: 50%;
		width: 100%;
		/* background-color 测试用 */
		/* background-color: darkred; */
	}

	#main-content .showStatistics .bottomEcharts .illPieTitle {
		position: absolute;
		top: -5%;
		left: 20%;
		height: 5%;
		width: 60%;
		font-size: 2.8vh;
		letter-spacing: 0.1vw;
		color: #58a0ee;
		/* background-color 测试用 */
		/* background-color: #FFFFFF; */
	}

	#main-content .showStatistics .bottomEcharts .pieEcharts {
		position: absolute;
		top: 6%;
		height: 94%;
		left: 0%;
		width: 100%;
		/* background-color 测试用 */
		/* background-color: #FFFFFF; */
	}
</style>
