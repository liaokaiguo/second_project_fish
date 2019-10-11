<template>
  <div class="background">


    <el-row>
      <el-col>
        <div class="mapContext" id="allmap"></div>
      </el-col>
      <el-col>
        <div class="centTitle"><a src="#">
          <el-menu
            class="el-menu-demo"
            mode="horizontal"
            @select="handleSelect"
            background-color="#ffffff"
            text-color="#000"
            active-text-color="#ffd04b">

            <el-submenu index="1">
              <template slot="title">
                <span>渔船位置</span>
                <i class="el-icon-location"></i>
              </template>
              <el-menu-item index="1-1">
                <i class="el-icon-refresh"></i>
                <span>实时刷新(半分钟)</span>
              </el-menu-item>
              <el-menu-item index="1-2">
                <i class="el-icon-edit-outline"></i>
                <span>条件选择</span>
              </el-menu-item>
            </el-submenu>

            <el-submenu index="2">
              <template slot="title">
                <span>违规作业分布图</span>
                <i class="el-icon-picture-outline"></i>
              </template>
              <el-submenu index="2-1">
                <template slot="title">围网</template>
                <el-menu-item index="2-1-1">热力图</el-menu-item>
                <el-menu-item index="2-1-2">分布图</el-menu-item>
                <el-menu-item index="2-1-3">卫星图</el-menu-item>
              </el-submenu>
              <el-menu-item index="2-2">拖网</el-menu-item>
              <el-menu-item index="2-3">张网</el-menu-item>
              <el-menu-item index="2-4">刺网</el-menu-item>
              <el-menu-item index="2-5">钓具</el-menu-item>
              <el-menu-item index="2-6">杂渔具</el-menu-item>
              <el-menu-item index="2-7">笼壶</el-menu-item>
            </el-submenu>

            <el-submenu index="3">
              <template slot="title">
                <span>渔船轨迹回放</span>
                <i class="el-icon-document"></i>
              </template>
              <el-menu-item index="3-1">
                <i class="el-icon-edit-outline"></i>
                <span>条件选择</span>
              </el-menu-item>
            </el-submenu>

            <el-submenu index="4">
              <template slot="title">
                <span>渔场区域选择</span>
                <i class="el-icon-search"></i>
              </template>
              <el-menu-item index="4-1">
                <i class="el-icon-picture-outline"></i>
                <span>渔场图层覆盖</span>
              </el-menu-item>
              <el-menu-item index="4-2">
                <i class="el-icon-picture-outline"></i>
                <span>渔场图层取消</span>
              </el-menu-item>
            </el-submenu>

          </el-menu>

        </a></div>
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

    <!--左侧工具栏 -->
    <div class="middle-tool-content">
      <div class="location-content">
        <el-tooltip effect="dark" content="坐标定位" placement="right" :hide-after="2000">
          <img src="../../assets/mapShowImg/location32.png" height="32" width="32" @click="locationVisible = !locationVisible" />
        </el-tooltip>
        <el-popover
          placement="right"
          title="坐标定位"
          width="300"
          trigger="manual"
          v-model="locationVisible">
          <el-form ref="reLocationForm" :model="reLocationForm" label-width="60px" >
            <el-form-item label="经度:" prop="lng">
              <el-input oninput ="value=value.replace(/[^0-9.]/g,'')" v-model="reLocationForm.lng"></el-input>
            </el-form-item>
            <el-form-item label="纬度:" prop="lat">
              <el-input oninput ="value=value.replace(/[^0-9.]/g,'')" v-model="reLocationForm.lat"></el-input>
            </el-form-item>
          </el-form>
          <div  style="text-align: right; margin: 0">
            <el-button size="mini" type="text" @click="locationVisible = false">取消</el-button>
            <el-button type="primary" size="mini" @click="reLocation">确定</el-button>
          </div>
        </el-popover>
      </div>

      <div class="distance-content">
        <el-tooltip effect="dark" content="测距" placement="right" :hide-after="2000">
          <img src="../../assets/mapShowImg/distance32.png" height="32" width="32" @click="openDistanceTool"/>
        </el-tooltip>
      </div>
      <div class="feedback-content">
        <el-tooltip effect="dark" content="意见反馈" placement="right" :hide-after="2000">
          <img src="../../assets/mapShowImg/feedback32.png" height="32" width="32"/>
        </el-tooltip>
      </div>
      <div class="online-content">
        <el-tooltip effect="dark" content="在线客服" placement="right" :hide-after = "2000">
          <img src="../../assets/mapShowImg/online32.png" height="32" width="32"/>
        </el-tooltip>
      </div>


      <div >
        xixix

      </div>
    </div>



    <!--右下角 渔船信息 -->
    <el-row class="bottom-content">
      <el-col :span="12" >
        <div id="bottomLeftEchartId" class="bottomleftEchart"></div>
      </el-col>
      <el-col class="bottom-right-class" :span="12" >
        <div >
          <el-table style="left:2%; width: 96%;  "
                    height="270"
                    :data=shipArr>
            <el-table-column
              prop="shipId"
              label="渔船编号"
              width="100">
            </el-table-column>
            <el-table-column
              prop="ship.shipName"
              label="渔船名"
              width="130">
            </el-table-column>
            <el-table-column
              prop="ship.jobType"
              label="作业方式"
              width="100">
            </el-table-column>
            <el-table-column
              prop="acqTime"
              label="定位时间"
              width="100">
            </el-table-column>
          </el-table>
        </div>
      </el-col>
    </el-row>

    <!--渔船位置信息选择弹出框-->
    <el-dialog title="渔船位置信息选择" :visible.sync="shipLocationDialog"
               @close="resetForm('shipLocationForm')">
      <el-form ref="shipLocationForm" :model="selectLocation" :label-width="formLabelWidth">
        <el-form-item label="渔船编号:" prop="boatId">
          <el-input v-model="selectLocation.boatId" class="select-input-data"
                    autocomplete="off" placeholder="请输入编号"></el-input>
        </el-form-item>

        <el-form-item label="航行时间:" prop="sailingTime">
          <el-date-picker class="select-input-data"
                          v-model="selectLocation.sailingTime"
                          value-format="yyyy-MM-dd HH:mm:ss"
                          default-time="12:30:30"
                          type="datetime"
                          placeholder="默认为当前时间"
                          align="right"
                          :picker-options="pickerOptions">
          </el-date-picker>
        </el-form-item>

        <el-form-item label="渔船作业类型:" prop="workType">
          <el-checkbox-group v-model="selectLocation.workType" class="select-input-data">
            <el-checkbox label="围网" name="workType"></el-checkbox>
            <el-checkbox label="拖网" name="workType"></el-checkbox>
            <el-checkbox label="张网" name="workType"></el-checkbox>
            <el-checkbox label="刺网" name="workType"></el-checkbox>
            <el-checkbox label="钓具" name="workType"></el-checkbox>
            <el-checkbox label="杂渔具" name="workType"></el-checkbox>
            <el-checkbox label="笼壶" name="workType"></el-checkbox>
          </el-checkbox-group>
        </el-form-item>

        <el-form-item label="渔船业务类型:" prop="businessType">
          <el-checkbox-group v-model="selectLocation.businessType" class="select-input-data">
            <el-checkbox label="养殖船" name="businessType"></el-checkbox>
            <el-checkbox label="国内捕捞船" name="businessType"></el-checkbox>
            <el-checkbox label="捕捞辅助船" name="businessType"></el-checkbox>
            <el-checkbox label="其他辅助船" name="businessType"></el-checkbox>
            <el-checkbox label="专业远洋渔船" name="businessType"></el-checkbox>
            <el-checkbox label="非专业远洋渔船" name="businessType"></el-checkbox>
          </el-checkbox-group>
        </el-form-item>

      </el-form>
      <div slot="footer" class="select-footer-class">
        <el-button @click="shipLocationDialog = false">取 消</el-button>
        <el-button type="primary" @click="locationConfirm('shipLocationForm')"
                   style="margin-left: 60px;margin-right: 60px">确定
        </el-button>
        <el-button @click="resetForm('shipLocationForm')">重置</el-button>
      </div>
    </el-dialog>

    <!--轨迹回放选择弹出框-->
    <el-dialog title="渔船航行信息选择" :visible.sync="shipTrackDialog" @close="resetForm('shipTrackForm')">
      <el-form ref="shipTrackForm" :model="selectTrack" :label-width="formLabelWidth" :rules="shipTrackFormRules">
        <el-form-item label="渔船编号:" prop="boatId">
          <el-input class="select-input-data" v-model="selectTrack.boatId"
                    autocomplete="off" placeholder="请输入编号"></el-input>
        </el-form-item>
        <el-form-item label="航行开始时间:" prop="beginDate">
          <el-date-picker class="select-input-data"
                          v-model="selectTrack.beginDate"
                          type="datetime"
                          placeholder="请输入时间"
                          value-format="yyyy-MM-dd HH:mm:ss"
                          align="right"
                          :picker-options="pickerOptions">
          </el-date-picker>
        </el-form-item>
        <el-form-item label="航行结束时间:" prop="endDate">
          <el-date-picker class="select-input-data"
                          v-model="selectTrack.endDate"
                          type="datetime"
                          placeholder="请输入时间"
                          value-format="yyyy-MM-dd HH:mm:ss"
                          align="right"
                          :picker-options="pickerOptions">
          </el-date-picker>
        </el-form-item>
        <el-form-item class="select-input-data" label="算法库:" prop="algorithmMode">
          <el-select v-model="selectTrack.algorithmMode" placeholder="请选择算法">
            <el-option
              v-for="item in algorithmOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" style="text-align: center;margin-top: 40px">
        <el-button @click="shipTrackDialog = false">取 消</el-button>
        <el-button type="primary" @click="trackReplay('shipTrackForm')"
                   style="margin-left: 60px;margin-right: 60px">航行轨迹显示
        </el-button>
        <el-button @click="resetForm('shipTrackForm')">重置</el-button>
      </div>
    </el-dialog>

  </div>


</template>
<script>
    export default {
        data() {
            return {
                user: {},
                timer: null,//定时器
                shipLocationDialog: false,//渔船位置信息弹窗
                shipTrackDialog: false,//航行轨迹弹窗
                formLabelWidth: '200px',//弹窗宽度
                algorithmOptions: [
                    {
                        value: '1',
                        label: 'XGBOOT'
                    }, {
                        value: '2',
                        label: '卷积神经网络'
                    }, {
                        value: '3',
                        label: '小波神经网络'
                    }],//算法库
                pickerOptions: {
                    shortcuts: [{
                        text: '今天',
                        onClick(picker) {
                            picker.$emit('pick', new Date());
                        }
                    }, {
                        text: '昨天',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() - 3600 * 1000 * 24);
                            picker.$emit('pick', date);
                        }
                    }, {
                        text: '一周前',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
                            picker.$emit('pick', date);
                        }
                    }],
                    disabledDate(time) {
                        return time.getTime() > Date.now();//如果没有后面的-8.64e6就是不可以选择今天的
                    }
                }, //时间选择项
                selectLocation: {
                    boatId: '',
                    sailingTime: '',
                    workType: [],
                    businessType: [],

                },//位置筛选条件名称
                selectTrack: {
                    boatId: '',
                    beginDate: '',
                    endDate: '',
                    algorithmMode: '',
                },//轨迹筛选条件名称
                shipTrackFormRules: {
                    boatId: [
                        {required: true, message: '请输入渔船编号', trigger: 'blur'},
                    ],
                    beginDate: [
                        {required: true, message: '请输入航行开始时间', trigger: 'blur'},
                    ],
                    endDate: [
                        {required: true, message: '请输入航行结束时间', trigger: 'blur'},
                    ],
                    algorithmMode: [
                        {required: true, message: '请选择预测算法', trigger: 'blur'},
                    ]
                }, //轨迹筛选表单规则

                /*渔船信息 相关*/
                shipArr: [],
                shipMarkerOpenOrClose: true,//渔船标注点开关

                /* 单只渔船特定时间段航行轨迹点 相关*/
                shipTrackArr: [],
                trackPathOpenOrClose: false, //轨迹路径开关
                trackPolyline: "", //路径线覆盖物
                trackMarker: "", // 路径上标注点覆盖物

                /*热力图 相关*/
                heatMapOverlay: [],// 热力图覆盖物
                heatMapOpenOrClose: false,// 热力图开关
                heatMapPoints: [],// 热力图的点数据

                /*网格相关*/
                centerPoint: '', //地图中心点
                centerLng: "125.20",//地图中心经纬度
                centerLat: "30.00",
                level: "8",//地图级别
                bounds: '',//当前地图的四个顶点
                span: '',//当前网格的跨度
                xgrids: [],//经线
                ygrids: [],//纬线
                beSelectBounds: [],
                existGrid: false,//是否存在网格

                /*左侧小工具栏 相关*/
                locationVisible:false,
                reLocationMarker:'',
                reLocationForm: {
                    lng: '',
                    lat: '',
                },

                /*Echarts 相关*/
                jobTypeCount:[], //各作业类型的数量
                bottomLeftOption : {
                    tooltip: {
                        trigger: 'axis',
                        axisPointer: {
                            type: 'shadow'
                        }
                    },
                    grid: {
                        left: '2%',
                        right: '2%',
                        bottom: '3%',
                        containLabel: true
                    },
                    xAxis: {
                        type: 'value',
                        inverse:'true', //反向
                        splitLine: {show: false},
                        axisLine: {
                            show: false
                        },
                        axisTick: {
                            show: false
                        }
                    },
                    yAxis: {
                        type: 'category',
                        position:'right',//靠右
                        data: ['围网', '拖网', '张网', '刺网', '钓具', '杂渔具', '笼壶'],
                        splitLine: {show: false},
                        axisLine: {
                            show: false
                        },
                        axisTick: {
                            show: false
                        },
                        offset: 10,
                        nameTextStyle: {
                            fontSize: 15
                        },
                        axisLabel: {
                            show: true,
                            textStyle: {
                                fontSize:16
                            }
                        }

                        },
                    series: [
                        {
                            name: '数量',
                            type: 'bar',
                            data: [100, 100, 100, 100, 100, 100, 100],
                            barWidth: 14,
                            barGap: 10,
                            smooth: true,
                            label: {
                                normal: {
                                    show: true,
                                    position: 'left',
                                    offset: [5, -2],
                                    textStyle: {
                                        color: '#F68300',
                                        fontSize: 16
                                    }
                                }
                            },
                            itemStyle: {
                                emphasis: {
                                    barBorderRadius:5,
                                },
                                normal:{
                                    barBorderRadius:5,
//每根柱子颜色设置
                                    color: function(params) {
                                        let colorList = [
                                            "#de97ee",
                                            "#59deee",
                                            "#2aee9f",
                                            "#a9ed58",
                                            "#FFDE76",
                                            "#ff8614",
                                            "#E43C59",
                                        ];
                                        return colorList[params.dataIndex];
                                    },
                                    opacity: 0.8,
                                },

                            },
                        }
                    ]
                },

            }

        },

        mounted() {

            this.mapReady(this.centerLng, this.centerLat, this.level);
            //获取后台渔船信息并标注
            this.getShipLocationArr();

        },
        methods: {
            /*获取日期格式yyyy-MM-dd HH:mm:ss*/
            getFormatTime(date) {
                // var date = new Date();
                var month = date.getMonth() + 1;
                var strDate = date.getDate();
                if (month >= 1 && month <= 9) {
                    month = "0" + month;
                }
                if (strDate >= 0 && strDate <= 9) {
                    strDate = "0" + strDate;
                }
                // todo 只有去年数据
                var currentDate = (date.getFullYear() - 1) + "-" + month + "-" + strDate
                    + " " + date.getHours() + ":" + date.getMinutes() + ":" + date.getSeconds();
                return currentDate;
            },

            /*返回*/
            reback() {
                this.$router.go(-1); //reback the last step
            },

            /* 重置表单 */
            resetForm(formName) {
                //表单重置
                this.$refs[formName].resetFields();

            },

            /*销毁定时器*/
            timerDestroy() {
                clearInterval(this.timer);
                this.timer = null;
            },

            /* 菜单选择 */
            handleSelect(key, keyPath) {
                console.log(key, keyPath);
                // 选择其他菜单确保 定时器销毁
                if (key != "1-1") {
                    this.timerDestroy();
                }

                switch (key) {

                    case "1-1": //间隔半分钟刷新渔船位置
                        this.timer = setInterval(() => {
                            this.getShipLocationArr();
                        }, 30000);
                        break;
                    case "1-2":
                        this.shipLocationDialog = true;// 渔船位置弹窗
                        break;

                    case "2-1-1":
                        this.getHeatMapPointsAndShow("围网");//围网热力图
                        break;
                    case "2-2":
                        this.getHeatMapPointsAndShow("拖网");//拖网热力图
                        break;
                    case "2-3":
                        this.getHeatMapPointsAndShow("张网");//张网热力图
                        break;
                    case "2-4":
                        this.getHeatMapPointsAndShow("刺网");//刺网热力图
                        break;
                    case "2-5":
                        this.getHeatMapPointsAndShow("钓具");//钓具热力图
                        break;
                    case "2-6":
                        this.getHeatMapPointsAndShow("杂渔具");//杂渔具热力图
                        break;
                    case "2-7":
                        this.getHeatMapPointsAndShow("笼壶");//笼壶热力图
                        break;

                    case "3-1":
                        this.shipTrackDialog = true;// 轨迹选择弹窗
                        break;

                    case "4-1":
                        this.initFisheryGrid();// 渔场网格显示
                        break;
                    case "4-2": {
                        //说明有网格覆盖层
                        if (this.existGrid === true) {
                            this.closeFisheryGrid();// 渔场网格取消
                        } else {
                            this.$message({
                                type: "warning",
                                message: '当前地图没有渔场网格覆盖!!!',
                                showClose: 'true',
                                duration: 4000,
                            })
                        }
                        break;
                    }

                }
            },

            /* 大地图显示 */
            mapReady(lng, lat, level) {
                //创建实例
                var map = new BMap.Map("allmap", {enableMapClick: false});
                //创建坐标点
                this.centerPoint = new BMap.Point(lng, lat);
                //初始化实例，传入坐标点并设置地图级别
                map.centerAndZoom(this.centerPoint, level);
                map.enableScrollWheelZoom(true);
                window.map = map;

            },

            /* 从后端获取渔船位置数据 放入shipArr，默认最近2分钟数据*/
            getShipLocationArr() {
                //先设置加载动画
                const shipLocationLoading = this.$loading({
                    lock: true,
                    text: 'Loading',
                    spinner: 'el-icon-loading',
                    background: 'rgba(0, 0, 0, 0.2)'
                });
                var date = new Date();
                var now = this.getFormatTime(date);
                date.setTime(date.getTime() - 1000 * 60 * 2);// 2分钟前
                var before = this.getFormatTime(date);
                console.log(now)
                console.log(before)
                this.axios({
                    method: 'post',
                    url: '/queryTrail',
                    data: {
                        shipId: "",
                        startTime: before,
                        endTime: now,
                        jobTypes: [],
                        businessTypes: [],
                    }
                }).then(res => {
                    shipLocationLoading.close();
                    this.jobTypeCount = res.data.jobtypeCount;
                    this.shipArr = res.data.tAcqDatas;
                    //画底部左半边echarts
                    this.drawBottomLeftCharts();

                    this.addShipMarker();
                }).catch((response) => {
                    console.log(response)
                })
            },

            /* 初始加载所有渔船标注位置 */
            addShipMarker() {
                this.shipMarkerOpenOrClose = true;
                //清除覆盖物，若有网格重新加载网格
                if (this.existGrid === true) {
                    this.deleteGrid()
                    map.clearOverlays();  //先关网格在清所有覆盖物 否则出错
                    this.heatMapOpenOrClose = false;// 另外两栏开关关闭
                    this.trackPathOpenOrClose = false;
                    this.initFisheryGrid()
                } else {
                    map.clearOverlays();
                    this.heatMapOpenOrClose = false;// 另外两栏开关关闭
                    this.trackPathOpenOrClose = false;
                }

                var point = new Array();//定义数组标注经纬信息
                var marker = new Array();//定义数组点对象信息
                var info = new Array();//定义悬浮提示信息
                //设置icon信息
                var width = 32;
                var height = 32;
                var imgSrc = require("../../assets/ship32.png"); //引入icon图片 本地图要用require
                var myIcon = new BMap.Icon(imgSrc, new BMap.Size(width, height));//配置icon
                for (var i = 0; i < this.shipArr.length; i++) {//遍历
                    point[i] = new window.BMap.Point(this.shipArr[i].longitude, this.shipArr[i].latitude);
                    marker[i] = new window.BMap.Marker(point[i], {icon: myIcon});//把icon和坐标添加到Marker中

                    /* 旧方法：覆盖物渔船标注一个个添加
                    // map.addOverlay(marker[i]);
                    // var label = new window.BMap.Label(this.shipArr[i].name);
                    // label.setStyle({  //设置提示框的样式
                    //     color: "#000",
                    //     fontSize: "12px",
                    //     backgroundColor: "#d5fdff",
                    //     border: "1px solid #ccc",
                    //     borderRadius: "2px",
                    //     padding: "2px 6px"
                    // });
                    // // marker[i].setLabel(label);
                    // info[i] = new window.BMap.InfoWindow(
                    //     "<div style='width:300px;'>"
                    //     + "<p>渔船编号：" + this.shipArr[i].ship.shipNo + "</p>"
                    //     + "<p>渔船名：" + this.shipArr[i].ship.shipName + "</p>"
                    //     + "<p>航行时速(m/s)：" + this.shipArr[i].speed + "</p>"
                    //     + "<p>定位时间：" + this.shipArr[i].acqTime + "</p>"
                    //     + "<p>地址经度：" + this.shipArr[i].longitude + "</p>"
                    //     + "<p>地址纬度：" + this.shipArr[i].latitude + "</p>"
                    //     + "</div>"
                    // );//悬浮提示信息
                    // this.addInfo(info[i], marker[i])

                     */
                }

                /*新方法:使用海量点*/
                if (document.createElement('canvas').getContext) {  // 判断当前浏览器是否支持绘制海量点
                    var _this = this;
                    var options = {
                        size: BMAP_POINT_SIZE_NORMAL,
                        // shape: BMAP_POINT_SHAPE_STAR,
                        color: '#2fed19'
                    }
                    var pointCollection = new BMap.PointCollection(point, options);  // 初始化PointCollection
                    pointCollection.addEventListener('click', function (e) {
                        // 循环查出值
                        for (var i = 0; i < _this.shipArr.length; i++) {
                            if (_this.shipArr[i].longitude == e.point.lng && _this.shipArr[i].latitude == e.point.lat) {// 经度==点击的,维度
                                break;
                            }
                        }
                        var targetPoint = new BMap.Point(e.point.lng, e.point.lat);

                        var opts = {
                            width: 300, // 信息窗口宽度
                            height: 70, // 信息窗口高度
                            title: "渔船信息", // 信息窗口标题
                        }
                        var Content = "<div style='width:300px;'>"
                            + "<p>渔船id：" + _this.shipArr[i].shipId + "</p>"
                            + "<p>渔船编号：" + _this.shipArr[i].ship.shipNo + "</p>"
                            + "<p>渔船名：" + _this.shipArr[i].ship.shipName + "</p>"
                            + "<p>渔船作业类型：" + _this.shipArr[i].ship.jobType + "</p>"
                            + "<p>渔船业务类型：" + _this.shipArr[i].ship.businessType + "</p>"
                            + "<p>航行时速(节)：" + _this.shipArr[i].speed + "</p>"
                            + "<p>定位时间：" + _this.shipArr[i].acqTime + "</p>"
                            + "<p>地址经度：" + _this.shipArr[i].longitude + "</p>"
                            + "<p>地址纬度：" + _this.shipArr[i].latitude + "</p>"
                            + "</div>";
                        var infoWindow = new BMap.InfoWindow(Content);  // 创建信息窗口对象
                        map.openInfoWindow(infoWindow, targetPoint); //开启信息窗口
                    });
                    map.addOverlay(pointCollection);  // 添加Overlay
                } else {
                    alert('请在chrome、safari、IE8+以上浏览器查看本示例');
                }

            },

            /* 表单--选择性显示渔船位置 */
            locationConfirm(formName) {
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        console.log('shipLocationForm submit!');
                        /*表单成功提交，则再处理逻辑*/
                        this.shipLocationDialog = false; //弹窗关闭

                        var date = new Date();
                        var sailingTime
                        var twoMinAgo

                        //时间不填，则默认时间为2分钟前到现在
                        if (this.selectLocation.sailingTime === '') {
                            sailingTime = this.getFormatTime(date)
                            date.setTime(date.getTime() - 120 * 1000);// 2分钟前
                            twoMinAgo = this.getFormatTime(date)
                        } else {

                            sailingTime = this.selectLocation.sailingTime;
                            var temp = new Date(sailingTime);
                            temp.setFullYear(temp.getFullYear() + 1)// todo 因为只有去年数据，这行之后不需要的
                            date.setTime(temp.getTime() - 120 * 1000);// 2分钟前
                            twoMinAgo = this.getFormatTime(date);
                        }

                        console.log(sailingTime);
                        console.log(twoMinAgo);
                        //先设置加载动画
                        const selectShipLocationLoading = this.$loading({
                            lock: true,
                            text: 'Loading',
                            spinner: 'el-icon-loading',
                            background: 'rgba(0, 0, 0, 0.2)'
                        });

                        //请求后台数据
                        this.axios({
                            method: 'post',
                            url: '/queryTrail',
                            data: {
                                shipId: this.selectLocation.boatId,
                                startTime: twoMinAgo,
                                endTime: sailingTime,
                                jobTypes: this.selectLocation.workType,
                                businessTypes: this.selectLocation.businessType,

                            }
                        }).then(res => {
                            selectShipLocationLoading.close();
                            this.jobTypeCount = res.data.jobtypeCount;
                            this.shipArr = res.data.tAcqDatas;
                            console.log("选择后的渔船数量" + this.shipArr.length)
                            if (this.shipArr.length === 0) {
                                this.$message({
                                    type: "warning",
                                    message: '当前筛选条件下没有任何渔船位置信息!!!',
                                    showClose: 'true',
                                    duration: 4000,
                                })
                            } else {
                                //画底部左半边echarts
                                this.drawBottomLeftCharts();
                                this.addShipMarker();
                            }

                        }).catch((response) => {
                            console.log(response)
                        })

                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                });
            },

            /* 从后端获取违规作业方式的热力图数据 放入heatMapPoints 默认最近5小时的数据*/
            getHeatMapPointsAndShow(jobType) {
                //先设置加载动画
                const HeatMapLoading = this.$loading({
                    lock: true,
                    text: 'Loading',
                    spinner: 'el-icon-loading',
                    background: 'rgba(0, 0, 0, 0.2)'
                });
                var date = new Date();
                var now = this.getFormatTime(date);
                date.setTime(date.getTime() - 1000 * 60 * 60 * 5);// 5小时前
                var before = this.getFormatTime(date);
                console.log(now)
                console.log(before)
                this.axios({
                    method: 'post',
                    url: '/queryHeatmap',
                    data: {
                        startTime: before,
                        endTime: now,
                        jobType: jobType,
                        idtfyFlag: '',

                    }
                }).then(res => {
                    HeatMapLoading.close();
                    console.log("热力图点数量" + res.data.length)
                    this.heatMapPoints = res.data
                    this.initHeatMap(this.heatMapPoints);

                }).catch((response) => {
                    console.log(response)
                })
            },

            /* 添加热力图覆盖物 */
            initHeatMap(points) {
                this.heatMapOpenOrClose = true;

                //清除覆盖物，若有网格重新加载网格
                if (this.existGrid === true) {
                    this.deleteGrid()
                    map.clearOverlays();  //先关网格在清所有覆盖物 否则出错
                    this.shipMarkerOpenOrClose = false;// 另外两栏开关关闭
                    this.trackPathOpenOrClose = false;
                    this.initFisheryGrid()
                } else {
                    map.clearOverlays();
                    this.shipMarkerOpenOrClose = false;// 另外两栏开关关闭
                    this.trackPathOpenOrClose = false;
                }
                this.heatMapOverlay = new BMapLib.HeatmapOverlay({

                    'radius': BMAP_POINT_SIZE_SMALL * 4,// 热力图的每个点的半径大小
                    'opacity': 0.8,// 热力的透明度0~1
                    'gradient': {// 其中 key 表示插值的位置0~1，value 为颜色值
                        0: 'rgb(102, 255, 0)',
                        .5: 'rgb(255, 170, 0)',
                        1: 'rgb(255, 0, 0)'
                    }
                })

                // 添加热力覆盖物
                map.addOverlay(this.heatMapOverlay);
                this.heatMapOverlay.setDataSet({data: points, max: 100});

            },

            /* 表单 --轨迹回放按钮*/
            trackReplay(formName) {
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        console.log('shipTrackForm submit!');
                        // 开关开启
                        this.trackPathOpenOrClose = true;
                        //弹框隐藏
                        this.shipTrackDialog = false;
                        //清除覆盖物，若有网格重新加载网格
                        if (this.existGrid === true) {
                            this.deleteGrid();
                            map.clearOverlays();  //先关网格在清所有覆盖物 否则出错
                            this.shipMarkerOpenOrClose = false;// 另外两栏开关关闭
                            this.heatMapOpenOrClose = false;
                            this.initFisheryGrid()
                        } else {
                            map.clearOverlays();
                            this.shipMarkerOpenOrClose = false;// 另外两栏开关关闭
                            this.heatMapOpenOrClose = false;
                        }

                        this.getShipTrackPointsArr();//后台获取位置数据

                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                });
            },

            /* 根据条件，从后端获取渔船轨迹点数据 shipTrackArr*/
            getShipTrackPointsArr() {
                //先设置加载动画
                const shipTrackLoading = this.$loading({
                    lock: true,
                    text: 'Loading',
                    spinner: 'el-icon-loading',
                    background: 'rgba(0, 0, 0, 0.2)'
                });
                this.axios({
                    method: 'post',
                    url: '/queryTrail',
                    data: {
                        shipId: this.selectTrack.boatId,
                        startTime: this.selectTrack.beginDate,
                        endTime: this.selectTrack.endDate,
                        jobTypes: [],
                        businessTypes: [],

                    }
                }).then(res => {
                    shipTrackLoading.close();
                    this.shipTrackArr = res.data.tAcqDatas;
                    console.log(this.shipTrackArr.length)
                    if (this.shipTrackArr.length === 0) {
                        this.$message({
                            type: "warning",
                            message: '当前筛选条件下没有渔船轨迹信息!!!',
                            showClose: 'true',
                            duration: 4000,
                        })
                    } else {
                        this.loadTrackPath();// 画轨迹线
                    }

                }).catch((response) => {
                    console.log(response)
                })

            },

            /* 轨迹折现显示 */
            loadTrackPath() {
                // 轨迹线设置
                var sy = new BMap.Symbol(BMap_Symbol_SHAPE_BACKWARD_OPEN_ARROW, {
                    scale: 0.5,//图标缩放大小
                    strokeColor: '#fff',//设置矢量图标的线填充颜色
                    strokeWeight: '2',//设置线宽
                });
                var icons = new BMap.IconSequence(sy, '10', '30');

                // 创建polyline对象
                var pois = [];
                for (var i = 0; i < this.shipTrackArr.length; i++) {//遍历添加轨迹点
                    pois.push(new BMap.Point(this.shipTrackArr[i].longitude, this.shipTrackArr[i].latitude));
                }
                this.trackPolyline = new BMap.Polyline(pois, {
                    enableEditing: false,//是否启用线编辑，默认为false
                    enableClicking: true,//是否响应点击事件，默认为true
                    icons: [icons],
                    strokeWeight: '5',//折线的宽度，以像素为单位
                    strokeOpacity: 0.5,//折线的透明度，取值范围0 - 1
                    strokeColor: "#18a45b" //折线颜色
                });

                map.addOverlay(this.trackPolyline);          //增加折线

                //自动设置缩放级别和视图中心，将所有的point在视图范围内展示
                var view = map.getViewport(eval(pois));
                var mapZoom = view.zoom;
                var centerPoint = view.center;
                map.centerAndZoom(centerPoint, mapZoom);

                /*轨迹点标注*/
                var point = new Array();//定义数组标注经纬信息
                this.trackMarker = new Array();//定义数组点对象信息
                var info = new Array();//定义悬浮提示信息
                //设置icon信息
                var width = 32;
                var height = 32;
                var imgSrc = require("../../assets/shipTrack.png"); //引入icon图片 本地图片要用require
                var myIcon = new BMap.Icon(imgSrc, new BMap.Size(width, height));//配置icon
                for (var i = 0; i < this.shipTrackArr.length; i++) {//遍历
                    point[i] = new window.BMap.Point(this.shipTrackArr[i].longitude, this.shipTrackArr[i].latitude);
                    this.trackMarker[i] = new window.BMap.Marker(point[i], {icon: myIcon});//把icon和坐标添加到Marker中
                    map.addOverlay(this.trackMarker[i]);
                    var label = new window.BMap.Label(this.shipTrackArr[i].name);
                    label.setStyle({  //设置提示框的样式
                        color: "#000",
                        fontSize: "12px",
                        backgroundColor: "#d5fdff",
                        border: "1px solid #ccc",
                        borderRadius: "2px",
                        padding: "2px 6px"
                    });
                    // marker[i].setLabel(label);
                    info[i] = new window.BMap.InfoWindow(
                        "<div style='width:300px;'>"
                        + "<p>渔船id：" + this.shipTrackArr[i].shipId + "</p>"
                        + "<p>渔船编号：" + this.shipTrackArr[i].ship.shipNo + "</p>"
                        + "<p>渔船名：" + this.shipTrackArr[i].ship.shipName + "</p>"
                        + "<p>渔船作业类型：" + this.shipTrackArr[i].ship.jobType + "</p>"
                        + "<p>渔船业务类型：" + this.shipTrackArr[i].ship.businessType + "</p>"
                        + "<p>航行时速(节)：" + this.shipTrackArr[i].speed + "</p>"
                        + "<p>定位时间：" + this.shipTrackArr[i].acqTime + "</p>"
                        + "<p>地址经度：" + this.shipTrackArr[i].longitude + "</p>"
                        + "<p>地址纬度：" + this.shipTrackArr[i].latitude + "</p>"
                        + "<p>作业方式：" + this.shipTrackArr[i].workMode + "</p>"
                        + "</div>"
                    );//悬浮提示信息
                    this.addInfo(info[i], this.trackMarker[i])
                }
            },

            /* 点击渔船 悬浮渔船信息 */
            addInfo(info, marker) {
                marker.addEventListener("click", function (e) {
                    var p = e.target
                    var point = new BMap.Point(p.getPosition().lng, p.getPosition().lat)
                    map.centerAndZoom(point, 17);
                    this.openInfoWindow(info)
                })
            },

            /* 菜单点击显示渔场网格，并添加事件*/
            initFisheryGrid() {
                this.existGrid = true;
                this.initProperty();
                this.initGrid();
                var _this = this //外部变量 放到map里用

                //添加移动后的点击事件
                map.addEventListener("dragend", _this.showFisheryGrid);
                //添加放大或缩小时的事件
                map.addEventListener("zoomend", _this.showFisheryGrid);
                //设置点击事件
                map.addEventListener("click", function (e) {
                    var point = e.point;
                    //获取当前点是在哪个区块内,获取正方形的四个顶点
                    var points = _this.getGrid(_this, point);
                    //判断当前区域是否已经被选中过，如果被选中过则取消选中
                    var key = '' + points[0].lng + points[0].lat + points[2].lng + points[2].lat;//使用两个点的坐标作为key
                    if (_this.beSelectBounds[key]) {
                        map.removeOverlay(_this.beSelectBounds[key]);
                        delete _this.beSelectBounds[key];
                        return;
                    }
                    var polygon = new BMap.Polygon(points, {strokeColor: "red", strokeWeight: 4, strokeOpacity: 0.5}); //正方形区域样式
                    map.addOverlay(polygon);
                    _this.beSelectBounds[key] = polygon;
                    // 文字信息框
                    var lng_center = (points[0].lng + points[2].lng) / 2  //点击网格的中心经度
                    var lat_center = (points[0].lat + points[2].lat) / 2  //点击网格的中心维度
                    var point_center = new BMap.Point(lng_center, lat_center);
                    var opts = {
                        width: 400,     // 信息窗口宽度
                        height: 130,     // 信息窗口高度
                        title: "渔场信息", // 信息窗口标题
                    };
                    // 窗口显示内容
                    var text = "<div style='width:300px;'>"
                        + "<p>渔场名称：(" + '舟山渔场' + _this.level + "纬" + lat_center + ')' + "</p>"
                        + "<p>位置（经纬度）：(" + lng_center + ',' + lat_center + ')' + "</p>"

                        + "</div>"
                    var infoWindow = new BMap.InfoWindow(text, opts);  // 创建信息窗口对象
                    this.openInfoWindow(infoWindow, point_center);
                });


            },

            /* 渔场网格取消后，后续处理逻辑*/
            closeFisheryGrid() {
                this.deleteGrid(); //先删除网格

                // 获取当前地图新坐标和大小
                var nowLng = map.getCenter().lng;
                var nowLat = map.getCenter().lat;
                var nowLevel = map.getZoom();

                //若原有渔船标注点 则重新显示
                if (this.shipMarkerOpenOrClose === true) {
                    this.addShipMarker();
                }
                //若有热力图 重新显示
                if (this.heatMapOpenOrClose === true) {
                    this.initHeatMap(this.heatMapOverlay.data.data);
                }
                //若原有渔船轨迹路径 则重新显示
                if (this.trackPathOpenOrClose === true) {
                    this.loadTrackPath();
                    let newPoint = new BMap.Point(nowLng, nowLat);
                    map.centerAndZoom(newPoint, nowLevel);
                }
            },
            /*删除网格,移除事件 因其他地方也有调用 单独写出*/
            deleteGrid() {
                this.existGrid = false;
                // 重新加载地图
                var newLng = map.getCenter().lng;
                var newLat = map.getCenter().lat;
                var newLevel = map.getZoom();
                var _this = this //外部变量 放到map里用
                //移除移动后的点击事件
                map.removeEventListener("dragend", _this.showFisheryGrid);
                //移除放大或缩小时的事件
                map.removeEventListener("zoomend", _this.showFisheryGrid);
                // 点击事件带参移除不了 ，直接重新加载
                this.mapReady(newLng, newLat, newLevel);
            },

            /* 网格子模块 显示渔场网格*/
            showFisheryGrid() {
                this.initProperty();
                this.initGrid();
            },
            /* 网格子模块 初始化当前地图的状态*/
            initProperty() {
                this.level = map.getZoom();
                this.bounds = {
                    x1: map.getBounds().getSouthWest().lng,
                    y1: map.getBounds().getSouthWest().lat,
                    x2: map.getBounds().getNorthEast().lng,
                    y2: map.getBounds().getNorthEast().lat
                };
                this.span = this.getSpan();//需要使用level属性
            },
            /* 网格子模块 初始化网格*/
            initGrid() {
                //将原来的网格线先去掉
                for (var i in this.xgrids) {
                    map.removeOverlay(this.xgrids[i]);
                }
                this.xgrids = [];
                for (var i in this.ygrids) {
                    map.removeOverlay(this.ygrids[i]);
                }
                this.ygrids = [];

                //获取当前网格跨度
                var span = this.span;
                // 控制网格的长和宽
                span.x = span.x * 0.8;
                span.y = span.y * 0.8;
                //初始化地图上的网格
                for (var i = this.bounds.x1 + (this.centerPoint.lng - this.bounds.x1) % span.x - span.x; i < this.bounds.x2 + span.x; i += span.x) {

                    var polyline = new BMap.Polyline([
                        new BMap.Point(i.toFixed(6), this.bounds.y1),
                        new BMap.Point(i.toFixed(6), this.bounds.y2)
                    ], {strokeColor: "black", strokeWeight: 1, strokeOpacity: 0.5});

                    this.xgrids.push(polyline);
                    map.addOverlay(polyline);
                }
                for (var i = this.bounds.y1 + (this.centerPoint.lat - this.bounds.y1) % span.y - span.y; i < this.bounds.y2 + span.y; i += span.y) {
                    var polyline = new BMap.Polyline([
                        new BMap.Point(this.bounds.x1, i.toFixed(6)),
                        new BMap.Point(this.bounds.x2, i.toFixed(6))
                    ], {strokeColor: "black", strokeWeight: 1, strokeOpacity: 0.5});
                    this.ygrids.push(polyline);
                    map.addOverlay(polyline);
                }
            },
            /* 网格子模块 获取网格的跨度*/
            getSpan() {
                var scale = 0.75;
                var x = 0.0005;
                /*网格大小随zoom变化*/
                // for (var i = this.level; i < 19; i++) {
                //     x *= 2;
                // }
                x *= 128 //固定死网格
                var y = parseFloat((scale * x).toFixed(5));
                return {x: x, y: y};
            },
            /* 网格子模块 返回当前点在所在区块的四个顶点*/
            getGrid(_this, point) {
                //先找出两条纵线坐标
                var xpoints = this.xgrids.map(function (polyline) {
                    return polyline.getPath()[0].lng;
                }).filter(function (lng) {
                    return Math.abs(lng - point.lng) <= _this.span.x;
                }).sort(function (a, b) {
                    return a - b;
                }).slice(0, 2);

                //再找出两条横线的坐标
                var ypoints = this.ygrids.map(function (polyline) {
                    return polyline.getPath()[0].lat;
                }).filter(function (lat) {
                    return Math.abs(lat - point.lat) <= _this.span.y;
                }).sort(function (a, b) {
                    return a - b;
                }).slice(0, 2);

                return [
                    new BMap.Point(xpoints[0], ypoints[0]),
                    new BMap.Point(xpoints[0], ypoints[1]),
                    new BMap.Point(xpoints[1], ypoints[1]),
                    new BMap.Point(xpoints[1], ypoints[0])
                ];

            },

            /*
            *左侧小工具栏 相关
            *
            * */
            /*坐标定位*/
            reLocation() {

                if(this.reLocationForm.lng !='' && this.reLocationForm.lat !=''){
                    map.removeOverlay(this.reLocationMarker);
                    var new_point = new BMap.Point(this.reLocationForm.lng,this.reLocationForm.lat);
                    this.reLocationMarker = new BMap.Marker(new_point);  // 创建标注
                    map.addOverlay(this.reLocationMarker);
                    map.panTo(new_point);
                    this.locationVisible =false;
                }else{
                    this.$message({
                        type: "error",
                        message: '请输入正确经纬度格式!!!',
                        showClose: 'true',
                        duration: 4000,
                    })
                }
                this.resetForm('reLocationForm');

            },

            /*打开测距工具*/
            openDistanceTool(){
                var myDis = new BMapLib.DistanceTool(map);
                myDis.open();
            },

            /*画底部左边Echarts*/
            drawBottomLeftCharts() {
                var bottomLeftCharts = this.$echarts.init(
                    document.getElementById("bottomLeftEchartId")
                );
                bottomLeftCharts.setOption(this.bottomLeftOption);
                bottomLeftCharts.setOption({ //数据添加
                    series: [{
                        data: [
                            {
                                value: this.jobTypeCount.围网,
                                name: "围网"
                            },
                            {
                                value: this.jobTypeCount.拖网,
                                name: "拖网"
                            },
                            {
                                value: this.jobTypeCount.张网,
                                name: "张网"
                            },
                            {
                                value: this.jobTypeCount.刺网,
                                name: "刺网"
                            },
                            {
                                value: this.jobTypeCount.钓具,
                                name: "钓具"
                            },
                            {
                                value:this.jobTypeCount.杂渔具,
                                name: "杂渔具"
                            },
                            {
                                value: this.jobTypeCount.笼壶,
                                name: "笼壶"
                            }
                        ]
                    }]
                })

            },

        }


    }
</script>
<style scoped>

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
    position: absolute;
    left: 0%;
    width: 100%;
    height: 43px;
    font-family: FZDHTJW--GB1-0;
    font-size: 43px;
    font-weight: normal;
    font-stretch: normal;
    letter-spacing: 5px;
    color: #000000;
    float: left;
    opacity: 0.5; /*透明度 -*/
  }

  .centTitle a:hover {
    opacity: 0.8; /*透明度 -*/
  }


  .rightleftIcon {
    position: absolute;
    left: 90%;
    margin-top: 10px;
    float: left;
    opacity: 0.8; /*透明度 -*/
  }

  .mapContext {

    margin-top: 0px;
    position: absolute;
    height: 1080px;
    width: 100%;
    float: left;
    /*background-color: #e5e9f2;*/
  }

  .select-input-data {
    text-align: left;
    float: left;
    width: 60%;
    font-size: 23px;
    margin-bottom: 10px;
  }

  .select-footer-class {
    text-align: center;
    margin-top: 5px;
  }

  .bottom-content {
    position: absolute;
    bottom: 2%;
    left: 50%;
    height: 25%;
    width: 50%;
    float: left;

  }
  .bottomleftEchart{
    width: 480px;
    height: 270px;
  }
  .bottom-right-class{
    background-image: url("../../assets/mapInfo.gif");
    background-size: 100% 100%;
    opacity: 0.8;
  }

  .middle-tool-content {
    position: absolute;
    top:40%;
    left: 2%;
    width: 3%;
    height: 20%;
    float: left;
    background-color: white;
    opacity: 0.8;
    border-radius: 10px;

  }
  .location-content{
    width: 100%;
    height: 28%;

  }
  .distance-content{
    width: 100%;
    height: 28%;
  }
  .feedback-content{
    width: 100%;
    height: 28%;
  }
  .online-content{
    width: 100%;
    height: 28%;
  }
  .middle-tool-content img {
    filter: grayscale(100%);
    opacity: 0.66;
  }

  .middle-tool-content img:hover {
    filter: none;
    opacity: 1;
    cursor: pointer;

  }


</style>
<style>
  /*右下角表格变透明*/
  .el-table,  .el-table tr {
    background-color: transparent !important;
  }
  .el-table__header th, .el-table__header tr {
    background-color: transparent !important;
    color: black !important;
    text-align: center !important;
  }
  .el-table__body td, .el-table__body th{
    background-color: transparent !important;
    color: black !important;
    padding:2px !important;
    text-align: center !important;
  }

</style>
