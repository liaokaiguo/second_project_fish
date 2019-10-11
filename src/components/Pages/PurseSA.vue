<template>
  <div>
    <div class="background"></div>

    <div class="content">
      <div class="centTitle">
        <p class="myp1">围网作业方式统计分析</p>
      </div>
      <div class="rightIcon">
        <span v-on:click="$router.back(-1)">
            <img class="goback" src="../../assets/rebackLastIcon.png" style="cursor:pointer" alt="返回">
          </span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
        <router-link to="/">
          <img class="gohome" src="../../assets/rebackMainIcon.png">
        </router-link>
      </div>
      <div class="setMenu">
        <div class="fish">
          <div>
            <form method="post">
              <label>渔场：</label>
              <select v-model="fishMode" class="selectST" id="fishGround" @change="showFishArea">
                <option v-for="option in options" :value="option.value" class="fishingBackG">
                  {{option.value}}
                </option>
              </select>
            </form>
          </div>
        </div>
        <div class="time">
          <form method="post">
            <label>日期：</label>
            <input id="fisrtTime" type="date" name="firstTime" v-model="fisrtTime"/>
            &nbsp;到&nbsp;
            <input id="lastTime" type="date" name="lastTime" v-model="lastTime" />
          </form>
        </div>
        <div class="button">
          <button class="buttonSearch" @click="SearCh">查询</button>
          <button class="buttonSave" @click="Save">保存</button>
          <button class="buttonReset" @click="ReSet">重置</button>
        </div>
      </div>
      <div class="showResult">
        <!--地图模块-->
        <div class="showMap">
          <div class="FishMap" id="FishGroundMap"></div>
        </div>
        <!--统计结果模块-->
        <div class="showStatistics">
          <div class="illegalOSBroken">
            <div class="illBrokenTitle">非法作业统计折线图</div>
            <div class="BrokenEcharts" id="BrokenEchartsId"></div>
          </div>
          <div class="illegalOSPercentage">
            <div class="illPercentageTitle">非法作业统计占比图</div>
            <div class="PercentageEcharts" id="PerIOSEchartsId"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
    //围网 fishing vessel statistic and analysis
    export default {
        data() {
            return {
                fishMode: '所有渔场',
                fisrtTime: '2019-07-15',
                lastTime: '2019-08-31',
                centerPoint: '',//地图中心点坐标
                centerLng: "121",//地图中心经纬度
                centerLat: "30.60",
                level: "6",//地图级别
                selectedBorderColor: 'red',//渔场区域选中颜色
                selectedBorderWidth: 4,//渔场区域选中框宽度
                forbidLable: 0,//是否禁止区域被清除
                defaultBorderColor: 'gray',//渔场区域默认边框颜色
                defaultBorderWidth: 2,
                //非法作业折线图选项
                BrokenOption: {
                    color: ["#f44"],
                    grid:{
                        left: "10%",
                        top: "10%",
                        width: "80%",
                        height: "70%",
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
                                show: true,
                                inside: true,
                                alignWithLabel: true
                            },
                            axisLine: {
                                lineStyle: {
                                    color: "#5bbdff",
                                    width: 2,
                                }
                            },
                            axisLabel: {
                                fontSize: 14,
                                color: "#5b9bd5",
                                rotate: 0,
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
                                    color: "#5bbdff",
                                    width: 2,
                                },
                            },
                            scale: false,
                            axisLabel: {
                                fontSize: 14,
                                color: "#5b9bd5"
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
                            smooth: true,
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
                        left: "10%",
                        top: "0%",
                        width: "80%",
                        height: "100%",
                    },
                    tooltip: {
                        trigger: "item",
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: "vertical",
                        left: "4%",
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
                            center: ["50%", "50%"],
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
                    color: ["#5b9bff", "#ed7d31",]
                },
                //渔场列表
                options: [
                    {value: '所有渔场'},
                    {value: '舟山渔场'},
                    {value: '舟外渔场'},
                    {value: '长江口渔场'},
                    {value: '江外渔场'},
                    {value: '吕泗渔场'},
                    {value: '大沙渔场'},
                    {value: '沙外渔场'},
                    {value: '鱼山渔场'},
                    {value: '鱼外渔场'},
                    {value: '温台渔场'},
                    {value: '温外渔场'},
                ],
                //渔场区域位置信息
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
                //存储占比图数据
                data: {
                    normal: {value: 0, name: '正常作业'},
                    illegal: {value: 0, name: '非法作业'},
                },
                /*存储返回的数据以及时间序列*/
                saveData:[],
            };
        },
        mounted() {
            this.initCharts();
            this.fishMap(this.centerLng,this.centerLat,this.level);
        },
        methods: {
            reback() {
                this.$router.go(-1); //reback the last step
            },
            initCharts(){
                //初始化图表
                this.dataAskDeal();
            },
            //显示单个渔场网格框（矩形区域）
            fishgrid(leftLng,downLat,rightLng,upLat,color,bordWidth,forbid) {
                var pStart,pEnd,rectangle;
                pStart = new BMap.Point(leftLng,downLat);
                pEnd = new BMap.Point(rightLng,upLat);
                rectangle = new BMap.Polygon([
                    new BMap.Point(pStart.lng,pStart.lat),
                    new BMap.Point(pEnd.lng,pStart.lat),
                    new BMap.Point(pEnd.lng,pEnd.lat),
                    new BMap.Point(pStart.lng,pEnd.lat)
                ],{strokeColor:color,strokeWeight:bordWidth,strokeStyle:'dashed'});
                rectangle.setFillColor('none');//设置覆盖物透明
                if(forbid){
                    rectangle.disableMassClear();
                }
                map.addOverlay(rectangle);
            },
            //显示区域直线
            linefish(startLng,startLat,endLng,endLat,color,bordWidth,forbid){
                var line;
                line = new BMap.Polyline([
                    new BMap.Point(startLng,startLat),
                    new BMap.Point(endLng,endLat)
                ],{strokeColor:color,strokeWeight:bordWidth,strokeStyle:'dashed'});
                if(forbid){
                    line.disableMassClear();
                }
                map.addOverlay(line);
            },
            //渔场地图
            fishMap(lng,lat,level) {
                var map = new BMap.Map('FishGroundMap',{enableMapClick: false}); //创建地图实例
                this.centerpoint = new BMap.Point(lng, lat);//创建点坐标
                map.addControl(new BMap.MapTypeControl({
                    mapTypes:[
                        BMAP_NORMAL_MAP,
                        BMAP_SATELLITE_MAP,
                        BMAP_HYBRID_MAP
                    ]
                }));
                map.centerAndZoom(this.centerpoint, level);//初始化地图，设置中心点坐标和地图级别
                map.enableScrollWheelZoom(true);//开启鼠标滚轮缩放
                window.map=map;//存储map变量
                var _this = this;
                //默认显示整个渔场范围
                this.fullShow();
                //点击显示渔场信息和经纬度
                map.addEventListener("click",function(click) {
                    if(click.point.lng >= 120.2 && click.point.lng <= 122.5 &&
                        click.point.lat >= 32 && click.point.lat <= 34){
                        map.clearOverlays();//清除渔场区域范围
                        var infoWindow = new BMap.InfoWindow("左经度:120.2 下纬度:32" +'<br />'+
                            "右经度:122.5 上纬度:34",{title:"吕泗渔场",width:290,height:100});
                        map.openInfoWindow(infoWindow,click.point);
                        _this.forbidLable=0;
                        _this.fishgrid(120.2,32,122.5,34,_this.selectedBorderColor,_this.selectedBorderWidth,_this.forbidLable);//this在map中的作用域出问题，需先声明
                    }
                    else if(click.point.lng >= 122.5 && click.point.lng <= 125 &&
                        click.point.lat >= 32 && click.point.lat <= 34){
                        map.clearOverlays();//清除渔场区域范围
                        var infoWindow = new BMap.InfoWindow("左经度:122.5 下纬度:32" +'<br />'+
                            "右经度:125 上纬度:34",{title:"大沙渔场",width:290,height:100});
                        map.openInfoWindow(infoWindow,click.point);
                        _this.forbidLable=0;
                        _this.fishgrid(122.5,32,125,34,_this.selectedBorderColor,_this.selectedBorderWidth,_this.forbidLable);//this在map中的作用域出问题，需先声明
                    }
                    else if(click.point.lng >= 122 && click.point.lng <= 125 &&
                        click.point.lat >= 31 && click.point.lat <= 32){
                        map.clearOverlays();//清除渔场区域范围
                        var infoWindow = new BMap.InfoWindow("左经度:122 下纬度:31" +'<br />'+
                            "右经度:125 上纬度:32",{title:"长江口渔场",width:290,height:100});
                        map.openInfoWindow(infoWindow,click.point);
                        _this.forbidLable=0;
                        _this.fishgrid(122,31,125,32,_this.selectedBorderColor,_this.selectedBorderWidth,_this.forbidLable);//this在map中的作用域出问题，需先声明
                    }
                    else if(click.point.lng >= 121.8 && click.point.lng <= 125 &&
                        click.point.lat >= 29.5 && click.point.lat <= 31){
                        map.clearOverlays();//清除渔场区域范围
                        var infoWindow = new BMap.InfoWindow("左经度:121.8 下纬度:29.5" +'<br />'+
                            "右经度:125 上纬度:31",{title:"舟山渔场",width:290,height:100});
                        map.openInfoWindow(infoWindow,click.point);
                        _this.forbidLable=0;
                        _this.fishgrid(121.8,29.5,125,31,_this.selectedBorderColor,_this.selectedBorderWidth,_this.forbidLable);//this在map中的作用域出问题，需先声明
                    }
                    else if(click.point.lng >= 121 && click.point.lng <= 125 &&
                        click.point.lat >= 28 && click.point.lat <= 29.5){
                        map.clearOverlays();//清除渔场区域范围
                        var infoWindow = new BMap.InfoWindow("左经度:121 下纬度:28" +'<br />'+
                            "右经度:125 上纬度:29.5",{title:"鱼山渔场",width:290,height:100});
                        map.openInfoWindow(infoWindow,click.point);
                        _this.forbidLable=0;
                        _this.fishgrid(121,28,125,29.5,_this.selectedBorderColor,_this.selectedBorderWidth,_this.forbidLable);//this在map中的作用域出问题，需先声明
                    }
                    else if(click.point.lng >= 120 && click.point.lng <= 125 &&
                        click.point.lat >= 27 && click.point.lat <= 28){
                        map.clearOverlays();//清除渔场区域范围
                        var infoWindow = new BMap.InfoWindow("左经度:120 下纬度:27" +'<br />'+
                            "右经度:125 上纬度:28",{title:"温台渔场",width:290,height:100});
                        map.openInfoWindow(infoWindow,click.point);
                        _this.forbidLable=0;
                        _this.fishgrid(120,27,125,28,_this.selectedBorderColor,_this.selectedBorderWidth,_this.forbidLable);//this在map中的作用域出问题，需先声明
                    }
                    else if(click.point.lng >= 125 && click.point.lng <= 128 &&
                        click.point.lat >= 32 && click.point.lat <= 34){
                        map.clearOverlays();//清除渔场区域范围
                        var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:32" +'<br />'+
                            "右经度:128 上纬度:34",{title:"沙外渔场",width:290,height:100});
                        map.openInfoWindow(infoWindow,click.point);
                        _this.forbidLable=0;
                        _this.fishgrid(125,32,128,34,_this.selectedBorderColor,_this.selectedBorderWidth,_this.forbidLable);//this在map中的作用域出问题，需先声明
                    }
                    else if(click.point.lng >= 125 && click.point.lng <= 128 &&
                        click.point.lat >= 31 && click.point.lat <= 32){
                        map.clearOverlays();//清除渔场区域范围
                        var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:31" +'<br />'+
                            "右经度:128 上纬度:32",{title:"江外渔场",width:290,height:100});
                        map.openInfoWindow(infoWindow,click.point);
                        _this.forbidLable=0;
                        _this.fishgrid(125,31,128,32,_this.selectedBorderColor,_this.selectedBorderWidth,_this.forbidLable);//this在map中的作用域出问题，需先声明
                    }
                    else if(click.point.lng >= 125 && click.point.lng <= 128 &&
                        click.point.lat >= 29.5 && click.point.lat <= 31){
                        map.clearOverlays();//清除渔场区域范围
                        var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:29.5" +'<br />'+
                            "右经度:128 上纬度:31",{title:"舟外渔场",width:290,height:100});
                        map.openInfoWindow(infoWindow,click.point);
                        _this.forbidLable=0;
                        _this.fishgrid(125,29.5,128,31,_this.selectedBorderColor,_this.selectedBorderWidth,_this.forbidLable);//this在map中的作用域出问题，需先声明
                    }
                    else if(click.point.lng >= 125 && click.point.lng <= 127 &&
                        click.point.lat >= 28 && click.point.lat <= 29.5){
                        map.clearOverlays();//清除渔场区域范围
                        var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:28" +'<br />'+
                            "右经度:127 上纬度:29.5",{title:"鱼外渔场",width:290,height:100});
                        map.openInfoWindow(infoWindow,click.point);
                        _this.forbidLable=0;
                        _this.fishgrid(125,28,127,29.5,_this.selectedBorderColor,_this.selectedBorderWidth,_this.forbidLable);//this在map中的作用域出问题，需先声明
                    }
                    else if(click.point.lng >= 125 && click.point.lng <= 127.5 &&
                        click.point.lat >= 27 && click.point.lat <= 28){
                        map.clearOverlays();//清除渔场区域范围
                        var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:27" +'<br />'+
                            "右经度:127.5 上纬度:28",{title:"温外渔场",width:290,height:100});
                        map.openInfoWindow(infoWindow,click.point);
                        _this.forbidLable=0;
                        _this.fishgrid(125,27,127.5,28,_this.selectedBorderColor,_this.selectedBorderWidth,_this.forbidLable);//this在map中的作用域出问题，需先声明
                    }
                    else{
                        map.clearOverlays();//清除渔场区域范围
                    }
                });
            },
            //通过文本框选择渔场区域在地图中显示
            showFishArea(){
                if(this.fishMode=='吕泗渔场'){
                    map.clearOverlays();//清除渔场区域范围
                    var infoWindow = new BMap.InfoWindow("左经度:120.2 下纬度:32" +'<br />'+
                        "右经度:122.5 上纬度:34",{title:"吕泗渔场",width:290,height:100});
                    var changeCenterPoint = new BMap.Point(121.3, 33);//创建点坐标
                    /*map.setCenter(changeCenterPoint);//设置地图中心点
                    map.setZoom(9);//设置地图级别*/
                    map.openInfoWindow(infoWindow,changeCenterPoint);//显示经纬度信息和渔场名称
                    this.forbidLable=0;
                    this.fishgrid(120.2,32,122.5,34,this.selectedBorderColor,this.selectedBorderWidth,this.forbidLable);//显示渔场范围
                }
                else if(this.fishMode=='大沙渔场'){
                    map.clearOverlays();//清除渔场区域范围
                    var infoWindow = new BMap.InfoWindow("左经度:122.5 下纬度:32" +'<br />'+
                        "右经度:125 上纬度:34",{title:"大沙渔场",width:290,height:100});
                    var changeCenterPoint = new BMap.Point(123.7, 33);//创建点坐标
                    /*map.setCenter(changeCenterPoint);//设置地图中心点
                    map.setZoom(9);//设置地图级别*/
                    map.openInfoWindow(infoWindow,changeCenterPoint);//显示经纬度信息和渔场名称
                    this.forbidLable=0;
                    this.fishgrid(122.5,32,125,34,this.selectedBorderColor,this.selectedBorderWidth,this.forbidLable);//显示渔场范围
                }
                else if(this.fishMode=='长江口渔场'){
                    map.clearOverlays();//清除渔场区域范围
                    var infoWindow = new BMap.InfoWindow("左经度:122 下纬度:31" +'<br />'+
                        "右经度:125 上纬度:32",{title:"长江口渔场",width:290,height:100});
                    var changeCenterPoint = new BMap.Point(123.5, 31.5);//创建点坐标
                    /*map.setCenter(changeCenterPoint);//设置地图中心点
                    map.setZoom(9);//设置地图级别*/
                    map.openInfoWindow(infoWindow,changeCenterPoint);//显示经纬度信息和渔场名称
                    this.forbidLable=0;
                    this.fishgrid(122,31,125,32,this.selectedBorderColor,this.selectedBorderWidth,this.forbidLable);//显示渔场范围
                }
                else if(this.fishMode=='舟山渔场'){
                    map.clearOverlays();//清除渔场区域范围
                    var infoWindow = new BMap.InfoWindow("左经度:121.8 下纬度:29.5" +'<br />'+
                        "右经度:125 上纬度:31",{title:"舟山渔场",width:290,height:100});
                    var changeCenterPoint = new BMap.Point(123.6, 30.5);//创建点坐标
                    /*map.setCenter(changeCenterPoint);//设置地图中心点
                    map.setZoom(9);//设置地图级别*/
                    map.openInfoWindow(infoWindow,changeCenterPoint);//显示经纬度信息和渔场名称
                    this.forbidLable=0;
                    this.fishgrid(121.8,29.5,125,31,this.selectedBorderColor,this.selectedBorderWidth,this.forbidLable);//显示渔场范围
                }
                else if(this.fishMode=='鱼山渔场'){
                    map.clearOverlays();//清除渔场区域范围
                    var infoWindow = new BMap.InfoWindow("左经度:121 下纬度:28" +'<br />'+
                        "右经度:125 上纬度:29.5",{title:"鱼山渔场",width:290,height:100});
                    var changeCenterPoint = new BMap.Point(123, 28.7);//创建点坐标
                    /*map.setCenter(changeCenterPoint);//设置地图中心点
                    map.setZoom(9);//设置地图级别*/
                    map.openInfoWindow(infoWindow,changeCenterPoint);//显示经纬度信息和渔场名称
                    this.forbidLable=0;
                    this.fishgrid(121,28,125,29.5,this.selectedBorderColor,this.selectedBorderWidth,this.forbidLable);//显示渔场范围
                }
                else if(this.fishMode=='温台渔场'){
                    map.clearOverlays();//清除渔场区域范围
                    var infoWindow = new BMap.InfoWindow("左经度:120 下纬度:27" +'<br />'+
                        "右经度:125 上纬度:28",{title:"温台渔场",width:290,height:100});
                    var changeCenterPoint = new BMap.Point(122.5, 27.5);//创建点坐标
                    /*map.setCenter(changeCenterPoint);//设置地图中心点
                    map.setZoom(9);//设置地图级别*/
                    map.openInfoWindow(infoWindow,changeCenterPoint);//显示经纬度信息和渔场名称
                    this.forbidLable=0;
                    this.fishgrid(120,27,125,28,this.selectedBorderColor,this.selectedBorderWidth,this.forbidLable);//显示渔场范围
                }
                else if(this.fishMode=='沙外渔场'){
                    map.clearOverlays();//清除渔场区域范围
                    var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:32" +'<br />'+
                        "右经度:128 上纬度:34",{title:"沙外渔场",width:290,height:100});
                    var changeCenterPoint = new BMap.Point(126.5, 33);//创建点坐标
                    /*map.setCenter(changeCenterPoint);//设置地图中心点
                    map.setZoom(9);//设置地图级别*/
                    map.openInfoWindow(infoWindow,changeCenterPoint);//显示经纬度信息和渔场名称
                    this.forbidLable=0;
                    this.fishgrid(125,32,128,34,this.selectedBorderColor,this.selectedBorderWidth,this.forbidLable);//显示渔场范围
                }
                else if(this.fishMode=='江外渔场'){
                    map.clearOverlays();//清除渔场区域范围
                    var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:31" +'<br />'+
                        "右经度:129 上纬度:32",{title:"江外渔场",width:290,height:100});
                    var changeCenterPoint = new BMap.Point(127, 31.5);//创建点坐标
                    /*map.setCenter(changeCenterPoint);//设置地图中心点
                    map.setZoom(9);//设置地图级别*/
                    map.openInfoWindow(infoWindow,changeCenterPoint);//显示经纬度信息和渔场名称
                    this.forbidLable=0;
                    this.fishgrid(125,31,128,32,this.selectedBorderColor,this.selectedBorderWidth,this.forbidLable);//显示渔场范围
                }
                else if(this.fishMode=='舟外渔场'){
                    map.clearOverlays();//清除渔场区域范围
                    var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:29.5" +'<br />'+
                        "右经度:128 上纬度:31",{title:"舟外渔场",width:290,height:100});
                    var changeCenterPoint = new BMap.Point(126.5, 30.5);//创建点坐标
                    /*map.setCenter(changeCenterPoint);//设置地图中心点
                    map.setZoom(9);//设置地图级别*/
                    map.openInfoWindow(infoWindow,changeCenterPoint);//显示经纬度信息和渔场名称
                    this.forbidLable=0;
                    this.fishgrid(125,29.5,128,31,this.selectedBorderColor,this.selectedBorderWidth,this.forbidLable);//显示渔场范围
                }
                else if(this.fishMode=='鱼外渔场'){
                    map.clearOverlays();//清除渔场区域范围
                    var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:28" +'<br />'+
                        "右经度:127 上纬度:29.5",{title:"鱼外渔场",width:290,height:100});
                    var changeCenterPoint = new BMap.Point(126, 28.5);//创建点坐标
                    /*map.setCenter(changeCenterPoint);//设置地图中心点
                    map.setZoom(9);//设置地图级别*/
                    map.openInfoWindow(infoWindow,changeCenterPoint);//显示经纬度信息和渔场名称
                    this.forbidLable=0;
                    this.fishgrid(125,28,127,29.5,this.selectedBorderColor,this.selectedBorderWidth,this.forbidLable);//显示渔场范围
                }
                else if(this.fishMode=='温外渔场'){
                    map.clearOverlays();//清除渔场区域范围
                    var infoWindow = new BMap.InfoWindow("左经度:125 下纬度:27" +'<br />'+
                        "右经度:127.5 上纬度:28",{title:"温外渔场",width:290,height:100});
                    var changeCenterPoint = new BMap.Point(126, 27.5);//创建点坐标
                    /*map.setCenter(changeCenterPoint);//设置地图中心点
                    map.setZoom(9);//设置地图级别*/
                    map.openInfoWindow(infoWindow,changeCenterPoint);//显示经纬度信息和渔场名称
                    this.forbidLable=0;
                    this.fishgrid(125,27,127.5,28,this.selectedBorderColor,this.selectedBorderWidth,this.forbidLable);//显示渔场范围
                }
                else {
                    map.clearOverlays();//清除渔场区域范围
                    //this.fullShow();
                }
            },
            //整个渔场范围显示
            fullShow(){
                this.forbidLable=1;
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
                var fullArea=new BMap.Polygon([
                    new BMap.Point(120.2,34),
                    new BMap.Point(120.2,32),//渔场外边缘坐标点
                    new BMap.Point(122,32),
                    new BMap.Point(122,31),
                    new BMap.Point(121.8,31),
                    new BMap.Point(121.8,29.5),
                    new BMap.Point(121,29.5),
                    new BMap.Point(121,28),
                    new BMap.Point(120,28),
                    new BMap.Point(120,27),
                    new BMap.Point(127.5,27),
                    new BMap.Point(127.5,28),
                    new BMap.Point(127,28),
                    new BMap.Point(127,29.5),
                    new BMap.Point(128,29.5),
                    new BMap.Point(128,34),
                ],{strokeColor:this.defaultBorderColor,strokeWeight:this.defaultBorderWidth,strokeStyle:'dashed'});
                fullArea.setFillColor('none');
                fullArea.disableMassClear();
                map.addOverlay(fullArea);
                /*画出渔场区域内的线段*/
                this.linefish(125,34,125,27,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);
                this.linefish(125,32,128,32,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);
                this.linefish(125,31,128,31,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);
                this.linefish(125,29.5,127,29.5,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);
                this.linefish(125,28,127,28,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);
                this.linefish(122.5,34,122.5,32,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);
                this.linefish(122,32,125,32,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);
                this.linefish(122,31,125,31,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);
                this.linefish(121.8,29.5,125,29.5,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);
                this.linefish(121,28,125,28,this.defaultBorderColor,this.defaultBorderWidth,this.forbidLable);
            },
            //画出两个Echarts图
            drawPurseCharts() {
                var brokenEChart = this.$echarts.init(
                    document.getElementById("BrokenEchartsId")
                );
                var percentageEChart = this.$echarts.init(
                    document.getElementById("PerIOSEchartsId")
                );
                brokenEChart.setOption(this.BrokenOption);
                brokenEChart.setOption({
                    xAxis:[{
                        data:this.saveData.time,
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
            },
            //查询方法
            SearCh () {
                //请求数据
                var startTime = this.getDate(this.fisrtTime);
                var endTime = this.getDate(this.lastTime);
                if (endTime.getTime() - startTime.getTime() < 0) {
                    alert("终止时间不得早于起始时间！");
                } else {
                    this.dataAskDeal();
                }
            },
            //重置方法
            ReSet () {
                this.fishMode='所有渔场';
                this.fisrtTime='2019-07-15';
                this.lastTime='2019-08-31';
                this.initCharts();//重置图表
                //重置渔场区域
                map.clearOverlays();//清除渔场区域范围
                map.reset();//恢复初始的中心点和级别
                this.fullShow();
            },
            //保存报告
            Save () {
                this.axios({
                    method: "post",
                    url: "/downloadExcel",
                    data: {
                        jobType :'围网',
                        startTime : this.fisrtTime + ' 00:00:00',
                        endTime : this.lastTime + ' 23:59:59',
                        byDay:1
                    },
                    responseType:'blob'
                }).then((response)=>{
                    console.log(response);
                    const link = document.createElement('a');
                    let blob = new Blob([response.data],{type:'application/vnd.ms-excel'});
                    link.style.display = 'none';
                    link.href = URL.createObjectURL(blob);
                    link.setAttribute('download','围网分析报告('+this.fisrtTime+'至'+this.lastTime+').xlsx');
                    document.body.appendChild(link);
                    link.click();
                    document.body.removeChild(link);
                }).catch((response)=> {
                    console.log(response);
                })
            },
            //时间函数
            getDate(datestr){
                var temp = datestr.split("-");
                var date = new Date(temp[0],temp[1]-1,temp[2]);
                return date;
            },
            //数据请求及返回数据处理
            dataAskDeal () {
                this.saveData.time=[];
                var i=0;//i作为time数组的索引
                var startTime = this.getDate(this.fisrtTime);
                var endTime = this.getDate(this.lastTime);
                while((endTime.getTime()-startTime.getTime())>=0) {
                    var year = startTime.getFullYear();
                    var x = startTime.getMonth() + 1;//JS中的月份是0-11
                    var month = x.toString().length == 1 ? "0" + x.toString() : x;

                    var day = startTime.getDate().toString().length == 1 ? "0" + startTime.getDate() : startTime.getDate();
                    //保存时间序列
                    this.saveData.time[i]=year + '-' + month + '-' + day;
                    i +=1;
                    startTime.setDate(startTime.getDate() + 1);
                }
                //this.saveData.time = this.time;
                this.axios({
                    method: "post",
                    url: "/getDataByMonthOrDay",
                    data: {
                        jobType :'围网',
                        startTime : this.fisrtTime + ' 00:00:00',
                        endTime : this.lastTime + ' 23:59:59',
                        byDay:1
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
                    //this.Polydata.series=response.data.illegal;
                    this.drawPurseCharts();
                }).catch((response)=>{
                    console.log(response);
                })
            },
        }
    };
</script>
<style scoped>
  /*the whole web background style*/
  .background {
    background-image: url("../../assets/bg.png");
    background-size: 100% 100%;
    height: 100%;
    position: absolute;
    width: 100%;
    background-repeat: no-repeat;
  }
  /*the center title sytle*/
  .content{
    position: absolute;
    width: 100%;
    height: 100%;
    overflow: hidden;
    z-index: 90;
  }
  .centTitle {
    font-weight: normal;
    font-stretch: normal;
    letter-spacing: 0.2vw;
    float: left;
    position: absolute;
    left: 30vw;
    cursor: pointer;
    width: 40vw;
    height: 5.5vh;
    top:1vh;
    margin-bottom: 0.3vh;
    font-family: FZDHTJW--GB1-0;
    font-size: 4.5vh;
    color: #58a0ee;
  }
  /*右上角按钮格式*/
  .rightIcon{
    position: absolute;
    right: 2vw;
    width: 15vw;
    height: 5.5vh;
    margin-top: 5vh;
    margin-bottom: 0.1vh;
  }
  .goback{
    cursor: pointer;
    position: absolute;
    right: 50%;
    height: 80%;
  }
  .gohome{
    cursor: pointer;
    position: absolute;
    right: 25%;
    height: 80%;
  }
  /*菜单栏*/
  .setMenu{
    position: absolute;
    width: 80vw;
    height: 6vh;
    left: 10vw;
    top: 12vh;
    margin-top: 1vh;
    margin-bottom: 1vh;
    color: #58a0ee;
    /* background-color 测试用 */
    /*background-color: #ffffff;*/
  }
  /*渔场菜单*/
  .fish{
    position: absolute;
    color:#58a0ee;
    font-size: 2.8vh;
    width: 18%;
    height: 100%;
  }
  /*渔场下拉菜单背景格式*/
  .fishingBackG{
    background-color: #0e0270;
    color:#58a0ee;
  }
  .selectST{
    width: 60%;
    height:5vh;
    color:#58a0ee;
    background-color:#0c034b;
    font-size: 2.8vh;
    border-color:#58a0ee ;
    border-width: 2px;
  }
  /*time为时间类别*/
  .time{
    position: absolute;
    left: 20%;
    width: 50%;
    height: 100%;
    font-size: 2.8vh;
  }
  #lastTime{
    border-color:#58a0ee;
    background-color:#0e0270;
    color:#58a0ee;
    width: 35%;
    height: 5vh;
  }
  #fisrtTime{
    border-color:#58a0ee;
    background-color:#0e0270;
    color:#58a0ee;
    width: 35%;
    height: 5vh;
  }
  /*按钮格式*/
  .button{
    position: absolute;
    left: 75%;
    width: 25%;
    height: 100%;
    font-size: 2.8vh;
  }
  /*查询按钮格式*/
  .buttonSearch{
    width:24%;
    height: 5vh;
    font-size: 2.8vh;
    background-color: #0e0270;
    color:#58a0ee;
    border-color:#58a0ee ;
    border-width: 2px;
    cursor: pointer;
  }
  /*重置按钮格式*/
  .buttonReset{
    width:24%;
    font-size: 2.8vh;
    background-color: #0e0270;
    color:#58a0ee;
    border-color:#58a0ee ;
    border-width: 2px;
    cursor: pointer;
    height: 5vh;
  }
  /*保存按钮格式*/
  .buttonSave{
    width:24%;
    font-size: 2.8vh;
    background-color: #0e0270;
    color:#58a0ee;
    border-color:#58a0ee ;
    border-width: 2px;
    cursor: pointer;
    height: 5vh;
  }
  /*结果显示区域格式*/
  .showResult{
    position: absolute;
    width: 90vw;
    height: 74vh;
    left: 5vw;
    top: 18vh;
    margin-top: 0vh;
    margin-bottom: 0vh;
    /*background-color: #FFFFFF;*/
  }
  /*地图显示区域*/
  .showMap {
    position: absolute;
    width: 60%;
    height: 95%;
    left: 2%;
    top: 5%;
    /* background-color 测试用 */
    /*background-color: #EFAB00;*/
  }
  .FishMap {
    position: absolute;
    width: 100%;
    height: 100%;
    overflow: hidden;
    font-family: '微软雅黑';
  }
  /*统计结果显示区域*/
  .showStatistics {
    position: absolute;
    width: 33%;
    height: 95%;
    right: 2%;
    top: 5%;
    /* background-color 测试用 */
    /*background-color: #FFFFFF;*/
  }
  .illBrokenTitle {
    width: 60%;
    height: 5%;
    /*font-family: MicrosoftYaHei;*/
    font-size: 2.8vh;
    letter-spacing: 0.1vw;
    color: #58a0ee;
    top: -5%;
    left: 20%;
    position: absolute;
  }
  /*非法作业折线图模块格式*/
  .illegalOSBroken {
    position: absolute;
    top: 0%;
    left: 0%;
    height: 50%;
    width: 100%;
    /* background-color 测试用 */
    /*background-color: greenyellow;*/
  }
  .BrokenEcharts{
    top:6%;
    width: 100%;
    height: 94%;
    position: absolute;
  }
  /*非法作业占比图模块格式*/
  .illegalOSPercentage {
    position: absolute;
    top: 50%;
    height: 50%;
    width: 100%;
    /* background-color 测试用 */
    /*background-color: darkred;*/
  }
  .illPercentageTitle{
    width: 60%;
    height: 5%;
    /*font-family: MicrosoftYaHei;*/
    font-size: 2.8vh;
    letter-spacing: 0.1vw;
    color: #58a0ee;
    top: -5%;
    left: 20%;
    position: absolute;
  }
  .PercentageEcharts{
    top:6%;
    width: 100%;
    height: 94%;
    position: absolute;
  }
  /*清楚日期框后的上下两个按钮*/
  input[type=date] {
    -moz-appearance:textfield;/*火狐浏览器*/
  }
  input[type=date]::-webkit-inner-spin-button,/*谷歌浏览器*/
  input[type=date]::-webkit-outer-spin-button {
    -webkit-appearance: none;
    margin: 0;
  }
  /*去掉删除按钮*/
  input[type="date"]::-webkit-clear-button {
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
  /*年月日格式*/
  input[type="date"]::-webkit-datetime-edit-year-field {
    color: #58a0ee;
    background-color: #0e0270;
  }
  input[type="date"]::-webkit-datetime-edit-month-field {
    color: #58a0ee;
    background-color: #0e0270;
  }
  input[type="date"]::-webkit-datetime-edit-day-field {
    color: #58a0ee;
    background-color: #0e0270;
  }
  /*控制年月日之间的斜线或短横线的*/
  input[type="date"]::-webkit-datetime-edit-text {
    color: #58a0ee;
    padding: 0.5em;
  }
</style>
