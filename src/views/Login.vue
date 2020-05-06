<template>
  <div id="login">
    <el-row>
      <el-col :span="6" :offset="9" class="loginBox">
        <el-form :label-position="labelPosition" label-width="80px">
          <h4 class="title">安心外卖管理后台</h4>
          <el-form-item label="用户名">
            <el-input
              v-model="form.phone"
              type="text"
              prefix-icon="el-icon-user"
              placeholder="请输入用户名"
            ></el-input>
          </el-form-item>
          <el-form-item label="密码">
            <el-input
              v-model="form.password"
              type="password"
              prefix-icon="el-icon-more-outline"
              placeholder="请输入密码"
            ></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" size="small" @click="login">登陆</el-button>
            <el-button size="small" @click="reset">重置</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>
<script>
export default {
  name: "Login",
  data() {
    return {
      form:{
        phone: "",
        password: ""
      },
      labelPosition: "top"
    };
  },
  methods: {
    login() {
      var _this = this;
      var that=this;
      var username = _this.form.phone;
      var password = _this.form.password;
      if (username === "" || password === "") {
        _this.$message.error("请输入用户名或密码！");
      } else {
        this.$http.post('http://www.smartdk.top:4000/takeaway_login',that.form,{emulateJSON:true}).then(function(res){
                        if (res.body=='400'){
                            that.$message({
                                message: '登陆失败，用户名或密码错误!',
                                type: 'error',
                                duration:5000
                            });
                            that.form={}
                        }else {
                            if (res.body[0][3]==='admin') {
                              that.$message({
                                message: '登陆成功！',
                                type: 'success',
                                duration:2000
                            });
                            localStorage.setItem("token", this.form.phone);
                            that.$router.push({path: '/home'});
                            that.form={}
                            } else {
                              that.$message({
                                message: '您不是管理员！',
                                type: 'error',
                                duration:2000
                            });
                            }
                        }
                        // eslint-disable-next-line no-unused-vars
                    },function(res){
                        that.$message.error('服务器当前错误');
                        that.form={}
                    });
      }
    },
    reset() {
      this.username = this.password = "";
    }
  },
  created() {
    this.$message.error("请登录");
  },
  mounted() {
    // document.body.style.backgroundColor = "#198";
  }
};
</script>
<style  scoped>
#login {
  width: 100%;
  height: 100%;
  position: fixed;
  background-size: 100% 100%;
  background: radial-gradient(circle at 33% 33%,rgba(149,176,249,1), rgba(149,176,249,0) 50%), radial-gradient(circle at 11% 11%,rgba(114,226,253,1), rgba(114,226,253,0) 50%), radial-gradient(circle at 79% 39%,rgba(184,150,255,1), rgba(184,150,255,0) 50%), radial-gradient(circle at 90% 90%,rgba(86,207,210,1), rgba(86,207,210,0) 50%), radial-gradient(circle at 10% 90%,rgba(168,112,253,1), rgba(168,112,253,0) 50%) ;
  /* background-image: linear-gradient(
    to bottom right,
    rgb(23, 211, 195),
    rgb(0, 162, 255)
  ); */
}
.loginBox {
  margin-top: 10%;
  padding: 20px;
  border: white 2px solid;
  border-radius: 15px;
  background-color: white;
}
.title {
  font-size: 20px;
  font-weight: 600;
  text-align: center;
}
</style>
