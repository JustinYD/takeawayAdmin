<template>
  <div class="home">
    <el-container>
      <el-header >
        <el-row class="title"> 
          <el-col :span="22">
            <h2 >安心外卖管理平台</h2>
          </el-col>
          <el-col :span="2">
            <h4 style="cursor:pointer" @click="loginOut">退出</h4>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24" class="bottomboder">1</el-col>
        </el-row>
      </el-header>
      <el-container>
        <el-aside width="200px">
          <tap-bar></tap-bar>
        </el-aside>
        <el-main class="mainBox">
          <router-view />
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
// @ is an alias to /src

import TapBar from "../components/TapBar";
export default {
  name: "Home",
  components: {
    TapBar
  },
  methods: {
    loginOut() {
      localStorage.removeItem("token");
      this.$router.push({ path: "/login" });
    }
  }
};
</script>
<style >
.title{
  background: linear-gradient(
    to bottom right,
    rgb(23, 211, 195),
    rgb(255, 0, 234));
  -webkit-background-clip: text;
  color: transparent;
  -webkit-text-fill-color: transparent;
  text-fill-color: transparent;
}

</style>