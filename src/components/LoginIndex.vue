<template>
  <div>
    <div id="main-bottom"></div>
    <div id="main">
      <div class="content">
        <div class="logo">
          <a href="http://www.jec36.com.cn/" target="_blank">
            <img src="../assets/img/cetc36_logo.png" alt="中国电子科技集团公司第三十六研究所">
          </a>
        </div>
        <div class="info">
          <p>
            <strong>联系方式</strong><br>
            Email:229893157@qq.com<br>
            电话:+86 15686058556<br>
            地址:浙江省嘉兴市中电科智慧园
          </p>
          <hr>
          <p>
            Copyright &copy; 2019 <a href="http://www.jec36.com.cn/" target="_blank">中电科36所</a>
            &nbsp;&nbsp;&nbsp;&nbsp;All Rights Reserved.
          </p>
        </div>
      </div>

      <div class="rightside">
        <div class="blur"></div>
        <!-- 登录框 -->
        <div class="loginFrame">
          <!-- 头部提示信息 -->
          <div class="loginTitle">
            <p class="p1">用户登录</p>
          </div>

          <div>
          <el-form ref="loginForm" :model="user" :rules="rules" status-icon  label-with="80px" >

            <el-form-item prop="name" class="loginInput">
              <el-input v-model="user.name" placeholder="用户名" prefix-icon="el-icon-user">
              </el-input>
            </el-form-item>

            <el-form-item prop="pass" class="loginInput">

              <el-input v-model="user.pass" type="password" placeholder="密码" prefix-icon="el-icon-lock">
              </el-input>
            </el-form-item>

            <el-form-item >
             <el-button  @click="login" class="loginCmt" >登&nbsp&nbsp&nbsp&nbsp录</el-button>
            </el-form-item>
          </el-form>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
    export default {
        name: "LoginIndex",
        data (){
            return {
                user: {
                    name:"",
                    pass:"",
                },
                rules: {
                    name: [
                        {required: true, message: '用户名不能为空', trigger: 'blur'}
                    ],
                    pass: [
                        {required: true, message: '密码不能为空', trigg: 'blur'}
                    ]
                }
            }
        },
        methods:{
            login(){
                this.$refs.loginForm.validate((valid) => {
                    if(valid){
                        this.axios({
                            method:"post",
                            url:"/check",
                            data:{
                                username:this.user.name,
                                password:this.user.pass,
                            }
                        }).then((response)=>{
                            if (response.data.status === 1) {
                                sessionStorage.setItem('ms_username',this.user.name);
                                    this.$notify({
                                        type: 'success',
                                        message: '欢迎你，' + this.user.name + '!',
                                        duration: 3000
                                    })
                                    this.$router.push("/welcome")

                            } else {
                                this.$message({
                                    type: 'error',
                                    message: '用户名或密码错误!!!',
                                    showClose: 'true',
                                    duration:5000,
                                })
                                this.resetForm('loginForm')

                            }
                        }).catch((response)=>{
                            console.log(response);
                        })
                    } else {
                        return false
                    }
                })

            },
            /*重置表单*/
            resetForm(formName) {
                //表单重置
                this.$refs[formName].resetFields();
            },

        }


    }
</script>

<style scoped>
  * {
    margin: 0;
    padding: 0;
  }

  body {
    color: #f3f0f0;
    font-size: 12px;
    font-family: "Microsoft YaHei", tahoma, verdana, sans-serif;
  }

  /* .example .pp 中间用空格隔开，表示后代选择器，选择的是.example内的.pp */
  /* <div class="example"><div class="pp">被选择的元素 <div>/div> */
  /* .example.pp选择的是class中同时包含example和pp的元素。 */
  /* <div class="example pp">被选择的元素 /div> */

  /* 主要内容 */
  #main-bottom {
    background-image: url('../assets/img/background_picr.jpg');
    background-position: center center;
    background-attachment: fixed;
    background-repeat: no-repeat;
    background-size: cover;
    overflow: hidden;
    position: absolute;
    width: 100%;
    height: 100%;
    /* 显示在底层 */
    z-index: 1;
  }

  #main {
    position: absolute;
    width: 100%;
    height: 100%;
    z-index: 90;
    color: #ffffff;
  }

  #main .content {
    position: absolute;
    top: 0;
    right: 38.2%;
    left: 0;
    bottom: 0;
    overflow: hidden;
  }

  #main .content .logo {
    float: left;
    display: inline-block;
    margin: 20px 0 0 30px;
  }

  #main .content .logo img {
    width: auto;
    height: 40px;
  }

  #main .content .info {
    position: absolute;
    text-align: left;
    bottom: 40px;
    left: 40px;
  }

  #main .content .info a:link,
  a:visited,
  a:active {
    text-decoration: none;
    color: #eaeaea;
  }

  #main .rightside {
    position: absolute;
    top: 0;
    right: 0;
    width: 38.2%;
    bottom: 0;
    overflow: hidden;
  }

  /* 灰色磨砂 */
  #main .rightside .blur {
    position: absolute;
    top: 0;
    right: 0;
    width: 100%;
    bottom: 0;
    overflow: hidden;
    background-color: #000;
    opacity: 0.3;
    z-index: 96;
  }

  /* 登录框 */
  #main .rightside .loginFrame {
    right: 20%;
    width: 60%;
    top: 25%;
    height: 300px;
    position: absolute;
    background-color: #FFFFFF;
    border: 1px solid #1F4882;
    z-index: 97;
  }

  #main .rightside .loginFrame .loginTitle {
    width: 86%;
    border-bottom: 2px solid #ee7700;
    margin-top: 15px;
    margin-bottom: 10px;
    margin-right: auto;
    margin-left: auto;
    text-align: left;
  }

  #main .rightside .loginFrame .loginTitle .p1 {
    display: inline-block;
    font-size: 18px;
    margin-top: 13px;
    margin-bottom: 8px;
    width: 86%;
    color: #ee7700;
  }

  #main .rightside .loginFrame .loginInput {
    margin-top: 25px;
    margin-bottom: 10px;
    margin-right: auto;
    margin-left: auto;
    position: relative;
    width: 86%;
  }

  #main .rightside .loginFrame .loginInput img {
    position: absolute;
    top: 12px;
    left: 8px;
  }

  #main .rightside .loginFrame .loginInput input {
    width: 100%;
    height: 42px;
    text-indent: 2.5rem;
  }

  #main .rightside .loginFrame .loginCmt {
    width: 86%;
    height: 45px;
    background-color: #ee7700;
    font-size: 20px;
    color: white;
    margin-top: 18px;
    margin-right: auto;
    margin-bottom: 10px;
    margin-left: auto;
  }

  #main .rightside .loginFrame .loginCmt a button {
    width: 100%;
    height: 45px;
    background-color: #ee7700;
    border: none;
    color: white;
    font-size: 20px;
  }
</style>

