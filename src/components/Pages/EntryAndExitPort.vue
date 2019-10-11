<template>
  <div class="background">
    <div>
      <el-row>
        <el-col :span="6">
          <div class="lefttext">渔船出入港查询</div>
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
         <el-col :span="24" class="searchBox">
          <div >
            <el-form ref="selectForm" :model="select" :inline="true">
              <el-form-item prop="boatId" >
                <el-input v-model="select.boatId"
                          placeholder="渔船编号">
                </el-input>
              </el-form-item>

              <el-form-item prop="inOrOut" >
                <el-select v-model="select.inOrOut" placeholder="出港/入港">
                  <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                  </el-option>
                </el-select>
              </el-form-item>

              <el-form-item  prop="passDate"  >
                <el-date-picker style="width: 230px"
                                v-model="select.passDate"
                                type="date"
                                placeholder="日期时间"
                                align="right"
                                default-time="12:00:00"
                                :picker-options="pickerOptions"
                                value-format="yyyy-MM-dd">
                </el-date-picker>
              </el-form-item>

              <el-form-item prop="portName" >
                <el-input v-model="select.portName"
                          placeholder="港口名">
                </el-input>
              </el-form-item>

              <el-form-item align="center">
                <el-button type="primary" @click="search(select)">搜索</el-button>
                <el-button  @click="resetForm('selectForm')">重置</el-button>
              </el-form-item>
            </el-form>
          </div>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="24" >
          <div class="dataBox">
            <el-table
            :data="tableData.slice((currentPage-1)*pageSize,currentPage*pageSize)"
            v-loading="tableDataLoading"
            element-loading-text="数据量较大，玩命加载中"
            style="width: 100%"
            border
            :header-cell-style="{color:'#333',fontFamily:'MicrosoftYaHeiUI',fontSize:'14px',fontWeight:900}">
              <el-table-column
                prop="shipId"
                label="渔船编号"
                width="260">
              </el-table-column>
              <el-table-column
                prop="iof"
                label="出港/入港"
                width="260">
                <template slot-scope="scope">
                  <span>{{scope.row.iof===-1?'出港':'入港'}}</span>
                </template>
              </el-table-column>
              <el-table-column
                prop="acqTime"
                label="时间"
                width="300">
              </el-table-column>
              <el-table-column
                prop="ship.portName"
                label="港口名"
                width="300">
              </el-table-column>
            </el-table>
          </div>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12" :offset="6">
          <!-- 分页器 -->
          <div class="pagingBox" style="margin-top:15px;">
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
      </el-row>

    </div>

  </div>
</template>
<script>
//seine（围网）fishing vessel statistic and analysis
export default {
  data() {
      return{
          currentPage: 1, // 当前页码
          total: 20, // 总条数
          pageSize: 10, // 每页的数据条数

          options: [{
              value: '-1',
              label: '出港'
          },  {
              value: '1',
              label: '入港'
          }],

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
              }]
          },
          select:{
              boatId:'',
              inOrOut:'',
              passDate:'',
              portName:'',
          },

          tableData:[],
          tableDataLoading: true,

      }

  },

    mounted(){
        this.initTableData();
    },

  methods: {

      /*获取日期yyyy-MM-dd*/
      getFormatDate(date) {

          var month = date.getMonth() + 1;
          var strDate = date.getDate();
          if (month >= 1 && month <= 9) {
              month = "0" + month;
          }
          if (strDate >= 0 && strDate <= 9) {
              strDate = "0" + strDate;
          }

          // todo 只有去年数据
          var currentDate = (date.getFullYear()-1) + "-" + month + "-" + strDate;
          return currentDate;
      },

      ///重置表单
      resetForm(formName) {
          this.$refs[formName].resetFields();
          this.initTableData();
      },
      /*页面初始加载获取后台数据*/
      initTableData(){
          this.tableDataLoading =true; //先loading动画
          var date = new Date();
          var nowDay = this.getFormatDate(date);
          console.log(nowDay);

          this.axios({
              method:"post",
              url:"/queryPortTrafficWithoutJoin",
              data:{
                  shipId :'',
                  iof : '', //目前数据库iof均为-1，原因不明
                  acqTime :  nowDay, //默认当天
                  portName : '',
              }
          }).then((response)=>{
              console.log("出入港数据量: "+ response.data.length)
              this.tableDataLoading = false;// loading动画去掉
              this.tableData= response.data;

          }).catch((response)=>{
              console.log(response);
          })

      },

      /*搜索特定船只信息*/
      search(select){
          this.tableDataLoading =true; //先loading动画

          if(this.select.passDate === null){// null会引发后端请求错误
              this.select.passDate ='';
          }
          // console.log(this.select.boatId);
          // console.log(this.select.inOrOut);
          // console.log(this.select.passDate);
          // console.log(this.select.portName);
          this.axios({
              method:"post",
              url:"/queryPortTrafficWithoutJoin",
              data:{
                  shipId : this.select.boatId,
                  iof : this.select.inOrOut, //目前数据库iof均为-1，原因不明
                  acqTime :  this.select.passDate,
                  portName :  this.select.portName,
              }
          }).then((response)=>{
              console.log("选择后出入港数据量: "+ response.data.length)
              this.tableDataLoading = false;// loading动画去掉
              this.tableData= response.data;

          }).catch((response)=>{
              console.log(response);
          })
      },

      //分页
      handleSizeChange(val) {
          // console.log(`每页 ${val} 条`);
          this.currentPage = 1;
          this.pageSize = val;
      },
      handleCurrentChange(val) {
          // console.log(`当前页: ${val}`);
          this.currentPage = val;
      }

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
  margin-top: 50px;
  float: left;
  }
.dataBox{
  margin-left: 340px;
  margin-top: 20px;
  float: left;
  background-color: white;
}
.pagingBox{
  float: right;
  background-color: white;
}





</style>
