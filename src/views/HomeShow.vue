<template>
  <div>
    <el-row justify="space-between" type="flex">
      <el-col :span="5">
        <div class="userTotal totalBox">
          <h3>总用户数</h3>
          <h1>{{userTotal}}</h1>
        </div>
      </el-col>
      <el-col :span="5">
        <div class="orderTotal totalBox">
          <h3>总订单数</h3>
          <h1>{{orderTotal}}</h1>
        </div>
      </el-col>
      <el-col :span="5">
        <div class="sendTotal totalBox">
          <h3>配送中</h3>
          <h1>{{sendTotal}}</h1>
        </div>
      </el-col>
      <el-col :span="5">
        <div class="finishTotal totalBox">
          <h3>已完成订单</h3>
          <h1>{{finishTotal}}</h1>
        </div>
      </el-col>
    </el-row>
  </div>
</template>
<script>
export default {
  data() {
    return {
      userTotal: 0,
      orderTotal: 0,
      sendTotal: 0,
      finishTotal: 0
    };
  },
  methods: {
    //获取用户列表
    getuser() {
      var that = this;
      this.$http.get("http://www.smartdk.top:4000/takeaway_userlist").then(
        function(res) {
          if (res.body!='fail') {
            that.userTotal=res.body.length 
          }
        },
        function(res) {
          console.log(res.status);
        }
      );
    },
    //获取订单列表
    getorder() {
      var that = this;
      this.$http.get("http://www.smartdk.top:4000/takeaway_orderlist").then(
        function(res) {
          if (res.body!='fail') {
            that.orderTotal=res.body.length 
          }
        },
        function(res) {
          console.log(res.status);
        }
      );
    },
    //获取成功订单列表
    getsuccess() {
      var that = this;
      this.$http.get("http://www.smartdk.top:4000/takeaway_successlist").then(
        function(res) {
          if (res.body!='fail') {
            that.finishTotal=res.body.length 
          }
        },
        function(res) {
          console.log(res.status);
        }
      );
    },
    //获取配送中订单列表
    getsend() {
      var that = this;
      this.$http.get("http://www.smartdk.top:4000/takeaway_sendlist").then(
        function(res) {
          if (res.body!='fail') {
            that.sendTotal=res.body.length 
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
    this.getorder();
    this.getsuccess();
    this.getsend();
  }
};
</script>
<style>
.totalBox {
  border-radius: 5px;
  padding: 10px;
  color: white;
  cursor: pointer;
}
.totalBox h1{
  font-size: 25px;
}
.totalBox h3{
  font-size: 15px;
}
.userTotal {
  background-image: linear-gradient(
    to bottom right,
    rgb(109, 205, 243),
    rgb(117, 22, 224)
  );
}
.userTotal:hover{
  box-shadow:0 5px 12px 0px rgb(109, 205, 243,0.8) ;
}
.orderTotal {
  background-image: linear-gradient(
    to bottom right,
    rgb(23, 211, 195),
    rgb(255, 0, 234)
  );
}
.orderTotal:hover{
  box-shadow:0 5px 12px 0px rgb(255, 0, 234,0.5) ;
}
.sendTotal {
  background-image: linear-gradient(
    to bottom right,
    rgb(23, 51, 211),
    rgb(183, 0, 255)
  );
}
.sendTotal:hover{
  box-shadow:0 5px 12px 0px rgb(23, 51, 211,0.5) ;
}
.finishTotal {
  background-image: linear-gradient(
    to bottom right,
    rgb(23, 211, 195),
    rgb(0, 162, 255)
  );
}
.finishTotal:hover{
  box-shadow:0 5px 12px 0px rgb(23, 211, 195,0.5) ;
}
</style>\