<template>
  <div>
    <el-row :gutter="20">
      <el-col :span="9" class="userorder">
        <el-row>
          <el-col :span="12">
            <el-input
              @click="search(keywords)"
              prefix-icon="el-icon-search"
              v-model="keywords"
              placeholder="请输入手机号/用户名查询"
              clearable
            ></el-input>
          </el-col>
          <el-col :span="4" :offset="1">
            <el-button round="round" class="addbtn">添加</el-button>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-table :data="search(keywords)" height="500" style="width: 100%">
              <el-table-column fixed prop="username" label="用户名" sortable></el-table-column>
              <el-table-column fixed prop="password" label="密码" sortable></el-table-column>
              <el-table-column fixed prop="phone" label="手机号" sortable></el-table-column>
              <el-table-column fixed prop="admin" label="权限" sortable></el-table-column>
              <el-table-column fixed="right" label="操作">
                <template slot-scope="scope">
                  <el-button @click="handleClick(scope.row)" type="text" size="small">查看</el-button>
                  <el-button type="text" size="small">编辑</el-button>
                </template>
              </el-table-column>
            </el-table>
          </el-col>
        </el-row>
      </el-col>
      <el-col :span="15" class="userorder" v-if="userOrder.length==0">
        <el-alert style="margin-top:30%;margin-bottom:30%" title="当前未选择查看用户或该用户订单为0！" type="error" show-icon></el-alert>
      </el-col>
      <el-col :span="15" class="userorder" v-else>
        <el-table
          :data="userOrder"
          height="600"
          style="width: 100%"
        >
          <el-table-column fixed prop="name" label="用户名" sortable></el-table-column>
          <el-table-column fixed prop="location" label="地址" sortable></el-table-column>
          <el-table-column fixed prop="phone" label="手机号" sortable></el-table-column>
          <el-table-column fixed prop="send" label="配送状态" sortable></el-table-column>
          <el-table-column fixed prop="success" label="是否完成" sortable></el-table-column>
          <el-table-column fixed prop="createtime" label="创建时间" sortable></el-table-column>
          <el-table-column fixed prop="finishtime" label="完成时间" sortable></el-table-column>
        </el-table>
      </el-col>
    </el-row>
  </div>
</template>
<script>
export default {
  data() {
    return {
      userData: [],
      userOrder: [],
      keywords: ""
    };
  },
  methods: {
    handleClick(row) {
      var that = this;
      this.$http
        .post(
          "http://www.smartdk.top:4000/takeaway_userorder",
          { phone: row.phone },
          { emulateJSON: true }
        )
        .then(
          function(res) {
            if (res.body != "fail") {
              that.userOrder = [];
              let data = res.data;
              for (let i = 0; i < data.length; i++) {
                let send = "未配送";
                let success = "未完成";
                if (data[i][7] == 1) {
                  send = "已配送";
                  success = "完成";
                } else {
                  if (data[i][5] == 1) {
                    send = "配送中";
                  }
                }
                that.userOrder.push({
                  name: data[i][1],
                  location: data[i][2],
                  send: send,
                  success: success,
                  phone: data[i][6],
                  createtime: data[i][9],
                  finishtime: data[i][9]
                });
              }
            } else {
              that.userOrder = [];
            }
          },
          function(res) {
            that.userOrder = [];
          }
        );
    },
    //搜索
    search(keywords) {
      return this.userData.filter(item => {
        if (item.username.includes(keywords)) {
          return item;
        } else if (item.phone.includes(keywords)) {
          return item;
        }
      });
    },
    //获取用户列表
    getuser() {
      var that = this;
      this.$http.get("http://www.smartdk.top:4000/takeaway_userlist").then(
        function(res) {
          if (res.body != "fail") {
            let data = res.data;
            that.userData = [];
            for (let i = 0; i < data.length; i++) {
              that.userData.push({
                username: data[i][0],
                password: data[i][1],
                phone: data[i][2],
                admin: data[i][3]
              });
            }
          }
        },
        function(res) {
          console.log(res.status);
        }
      );
    }
  },
  created() {
    this.getuser();
  }
};
</script>
<style>
/* pageFullScreen在index.css */
.addbtn {
  color: white;
  background-image: linear-gradient(
    to bottom right,
    rgb(23, 151, 211),
    rgb(255, 0, 200)
  );
}
.userorder{
  box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04)
}
</style>
