<template>
  <div class="background">
    <div>
      <el-row>
        <el-col :span="6">
          <div class="lefttext">渔船作业方式统计及查询</div>
        </el-col>
        <el-col :span="12">
          <div class="centTitle">渔船作业方式智能识别系统</div>
        </el-col>
        <el-col :span="6">
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
    </div>
    <div>
      <el-row>
         <el-col :span="24" >
          <div >
            <el-form ref="selectForm" :model="select" :inline="true" class="searchBox">
              <el-form-item prop="startDate" >
                <el-date-picker
                  v-model="select.startDate"
                  type="date"
                  placeholder="作业开始日期"
                value-format="yyyy-MM-dd">
                </el-date-picker>
              </el-form-item>

              <el-form-item prop="legal" >
                <el-select v-model="select.legal" placeholder="是否合规">
                  <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                  </el-option>
                </el-select>
              </el-form-item>

              <el-form-item prop="workMode" >
                <el-select v-model="select.workMode" placeholder="作业方式">
                  <el-option
                    v-for="item in workOptions"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                  </el-option>
                </el-select>
              </el-form-item>

              <el-form-item align="center">
                <el-button type="primary" @click="search(select)">查询</el-button>
                <el-button  @click="resetForm('selectForm')">重置</el-button>
              </el-form-item>
            </el-form>
          </div>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="16" >
          <div class="dataBox">
            <el-table
            :data="tableData.slice((currentPage-1)*pageSize,currentPage*pageSize)"
            style="width: 100%"
            border
            :header-cell-style="{color:'#333',fontFamily:'MicrosoftYaHeiUI',fontSize:'14px',fontWeight:900}">
              <el-table-column
                prop="legal"
                label="是否合规"
                width="120">
                <template slot-scope="scope">
                  <span>{{scope.row.idtfyFlag=== 1?'正常作业':'违规作业'}}</span>
                </template>
              </el-table-column>
              <el-table-column
                prop="ship.shipNo"
                label="渔船编号"
                width="200">
              </el-table-column>
              <el-table-column
                prop="idtfyJobtype"
                label="作业方式"
                width="120">
              </el-table-column>
              <el-table-column
                prop="startTime"
                label="开始时间"
                width="200">
              </el-table-column>
              <el-table-column
                prop="endTime"
                label="结束时间"
                width="200">
              </el-table-column>
              <el-table-column
                prop="jobTime"
                label="作业持续时间"
                width="140">
              </el-table-column>
            </el-table>
          </div>
          <!-- 分页器 -->
          <div class="pagingBox" >
            <el-pagination align='cneter' @size-change="handleSizeChange"
                           @current-change="handleCurrentChange"
                           :current-page="currentPage"
                           :page-sizes="[1,5,10,20]"
                           :page-size="pageSize"
                           layout="total, sizes, prev, pager, next, jumper"
                           :total="tableData.length">
            </el-pagination>
          </div>
        </el-col>

        <el-col :span="8">
          <div class="wmEchartsBox" id="workModeEcharts" >
          </div>
        </el-col>
      </el-row>

    </div>

  </div>
</template>
<script>
    import echarts from 'echarts'
export default {
  data() {
      return{
          currentPage: 1, // 当前页码
          total: 20, // 总条数
          pageSize: 10, // 每页的数据条数

          options: [{
              value: '1',
              label: '正常作业'
          },  {
              value: '0',
              label: '违规作业'
          }],
          workOptions: [{
              value: '拖网',
              label: '拖网'
          },  {
              value: '张网',
              label: '张网'
          }, {
              value: '刺网',
              label: '刺网'
          }, {
              value: '围网',
              label: '围网'
          }, {
              value: '钓网',
              label: '钓网'
          }, {
              value: '拉网',
              label: '拉网'
          }],

          select:{
              startDate:'',
              legal:'',
              workMode:'',
          },

          // tableData: [{
          //     legal: '正常作业',
          //     boatid: 'MESS3377373',
          //     workmode: '拖网',
          //     starttime: '2019-08-08 12:22:33',
          //     endtime: '2019-08-08 19:22:33',
          //     duration:'7h'
          // },
          //     {
          //     legal: '正常作业',
          //     boatid: 'MSDS33567',
          //     workmode: '拖网',
          //     starttime: '2019-08-18 10:22:33',
          //     endtime: '2019-08-18 14:22:33',
          //     duration:'4h'
          // },
          //     {
          //     legal: '正常作业',
          //     boatid: 'WWDSQ1233473',
          //     workmode: '张网',
          //     starttime: '2019-08-12 08:22:33',
          //     endtime: '2019-08-12 18:22:33',
          //     duration:'10h'
          // },
          //     {
          //     legal: '违规作业',
          //     boatid: 'WSDW987876',
          //     workmode: '刺网',
          //     starttime: '2019-08-31 20:22:33',
          //     endtime: '2019-08-31 23:22:33',
          //     duration:'3h'
          // },
          //     {
          //     legal: '违规作业',
          //     boatid: 'WSDW987876',
          //     workmode: '刺网',
          //     starttime: '2019-08-31 20:22:33',
          //     endtime: '2019-08-31 23:22:33',
          //     duration:'3h'
          // },
          //     {
          //     legal: '违规作业',
          //     boatid: 'WSDW987876',
          //     workmode: '刺网',
          //     starttime: '2019-08-31 20:22:33',
          //     endtime: '2019-08-31 23:22:33',
          //     duration:'3h'
          // },
          //     {
          //     legal: '违规作业',
          //     boatid: 'WSDW987876',
          //     workmode: '刺网',
          //     starttime: '2019-08-31 20:22:33',
          //     endtime: '2019-08-31 23:22:33',
          //     duration:'3h'
          // },
          //     {
          //     legal: '违规作业',
          //     boatid: 'WSDW987876',
          //     workmode: '刺网',
          //     starttime: '2019-08-31 20:22:33',
          //     endtime: '2019-08-31 23:22:33',
          //     duration:'3h'
          // },
          //     {
          //     legal: '违规作业',
          //     boatid: 'WSDW987876',
          //     workmode: '刺网',
          //     starttime: '2019-08-31 20:22:33',
          //     endtime: '2019-08-31 23:22:33',
          //     duration:'3h'
          // },
          //     {
          //     legal: '违规作业',
          //     boatid: 'WSDW987876',
          //     workmode: '刺网',
          //     starttime: '2019-08-31 20:22:33',
          //     endtime: '2019-08-31 23:22:33',
          //     duration:'3h'
          // },
          //     {
          //     legal: '违规作业',
          //     boatid: 'WSDW987876',
          //     workmode: '刺网',
          //     starttime: '2019-08-31 20:22:33',
          //     endtime: '2019-08-31 23:22:33',
          //     duration:'3h'
          // }],

           tableData:[],
          workModeEchartsOption: {
              tooltip: {
                  trigger: "item",
                  formatter: "{a} <br/>{b}: {c} ({d}%)"
              },
              legend: {
                  orient: "horizontal",
                  x: "center",
                  top: "5%",
                  data: ["拖网", "刺网", "围网", "其它"],
                  textStyle: {
                      color: "default",
                      fontSize: "26",
                  }
              },
              series: [
                  {
                      name: "统计分析",
                      type: "pie",
                      center: ["50%", "60%"],
                      radius: ["40%","70%"],
                      avoidLabelOverlap: false,
                      label: {
                          normal: {
                              show: false,
                              position: "center"
                          },
                          emphasis: {
                              show: true,
                              textStyle: {
                                  fontSize: "40",
                                  fontWeight: "bold"
                              }
                          }
                      },
                      labelLine: {
                          normal: {
                              show: false
                          }
                      },
                      data: [
                          { value: 300, name: "拖网" },
                          { value: 300, name: "刺网" },
                          { value: 300, name: "围网" },
                          { value: 300, name: "其它" }
                      ]
                  }
              ],
              color: ["#6bc1ff", "#3cedbc", "#eeb110", "#ef09ed"]
          },


      }

  },
    mounted() {
        this.drawGraph();
        this.initWorkModeStatistic();
    },
    methods: {
        //分页
        handleSizeChange(val) {
            // console.log(`每页 ${val} 条`);
            this.currentPage = 1;
            this.pageSize = val;
        },
        handleCurrentChange(val) {
            // console.log(`当前页: ${val}`);
            this.currentPage = val;
        },

        /*画饼图*/
        drawGraph() {
            let myEcharts = this.$echarts.init(document.getElementById("workModeEcharts"));
            myEcharts.setOption(this.workModeEchartsOption);
            //获取后端数据
            this.axios(
                {
                    method:'post',
                    url:'/statisShipJob',
                    data:{
                        dateTime:"",
                        idtfyFlag:"",
                    }
                }).then(res => {
                myEcharts.hideLoading(); //加载出来隐藏加载动画
                myEcharts.setOption({  //数据添加
                    series: [{
                        data: [
                            {value: res.data.tuoWang, name: "拖网"},
                            {value: res.data.ciWang, name: "刺网"},
                            {value: res.data.weiWang, name: "围网"},
                            {value: res.data.other, name: "其它"}]
                    }]
                })
            })

        },
        beforeDestroy() {
            if (this.myEcharts) {
                this.myEcharts.clear();
            }
        },

        /*重置表单*/
        resetForm(formName) {
            this.$refs[formName].resetFields();
            this.initWorkModeStatistic();
            this.drawGraph();
        },

        /*列表数据初始加载*/
        initWorkModeStatistic(){
            this.axios({
                method:"post",
                url:"/queryShipJob",
                data:{
                    dateTime:"" ,
                    idtfyJobtype:"",
                    idtfyFlag: "",
                }
            }).then((response)=>{
                 // console.log(response.data)
                this.tableData= response.data;
            }).catch((response)=>{
                console.log(response);
            })
        },

        /*按条件查询*/
        search(select) {
            //列表数据更新
            console.log(this.select.startDate)
            console.log(this.select.workMode)
            console.log(this.select.legal)
            this.axios({
                method:"post",
                url:"/queryShipJob",
                data:{
                    dateTime : this.select.startDate ,
                    idtfyJobtype : this.select.workMode,
                    idtfyFlag :  this.select.legal,
                }
            }).then((response)=>{

                this.tableData= response.data;

            }).catch((response)=>{
                console.log(response);
            })

            //饼图数据更新
            let myEcharts = this.$echarts.init(document.getElementById("workModeEcharts"));
            this.axios(
                {
                    method:'post',
                    url:'/statisShipJob',
                    data:{
                        dateTime:this.select.startDate ,
                        idtfyFlag:this.select.legal,
                    }
                }).then(res => {
                myEcharts.hideLoading(); //加载出来隐藏加载动画
                myEcharts.setOption({  //数据添加
                    series: [{
                        data: [
                            {value: res.data.tuoWang, name: "拖网"},
                            {value: res.data.ciWang, name: "刺网"},
                            {value: res.data.weiWang, name: "围网"},
                            {value: res.data.other, name: "其它"}]
                    }]
                })
            })
        },

    },


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
  width: 586px;
  height: 43px;
  font-family: FZDHTJW--GB1-0;
  font-size: 43px;
  font-weight: normal;
  font-stretch: normal;
  letter-spacing: 5px;
  color: #58a0ee;
  margin-top: 20px;
  margin-left: 200px;
  float: left;
}
.lefttext {
  margin-left: 70px;
  margin-top: 40px;
  float: left;
  font-size: 32px;
  color: #ffffff;
}
.rightleftIcon {
  margin-left: 300px;
  margin-top: 50px;
  float: left;
}
.searchBox{
  margin-left: 100px;
  margin-top: 50px;
  float: left;
  }
.dataBox{
  margin-left: 100px;
  margin-top: 20px;
  float: left;
  background-color: white;
}
.wmEchartsBox{
  margin-left: 0px;
  width: 400px;
  height: 400px;
  float: left;
}
.pagingBox{
  position: relative;
  right: 16%;
  float: right;
  background-color: white;
  margin-top: 15px;
}





</style>
